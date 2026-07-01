# AI Feature Specification: Waitlist Confirmation Probability Predictor

This specification outlines the integration of an AI-driven Waitlist Confirmation Predictor on the IRCTC train search interface.

---

## Problem It Solves
When direct tickets are unavailable, passengers must book waitlist tickets (e.g., WL 14). However, there is zero visibility into whether a waitlisted ticket will eventually confirm. This leads to massive travel anxiety, causing users to book multiple backup tickets (which increases railway system load and requires refund processing) or cancel journeys altogether. This AI feature addresses the seat selection and waitlist visibility issues related to booking flows. (Refer to `PROBLEMS.md` Section 3).

---

## Proposed Feature — User Perspective
When a user searches for a train and sees that the status is waitlisted (e.g., "WL 15 / WL 9" for Sleeper class), the system displays a color-coded percentage badge next to the status. For example:
- **WL 9 (85% Confirmation Chance)** in a green badge.
- **WL 45 (32% Confirmation Chance)** in a red badge.

This percentage represents the statistical likelihood that the ticket will be confirmed or upgraded to RAC (Reservation Against Cancellation) by the time the train's final chart is prepared. The user can make an informed decision immediately whether to book this train, wait, or search for alternative routes.

---

## Model or API Choice
We will use a **Custom XGBoost Classifier** (e.g., `xgboost` python library) trained on historical reservation patterns and deployed as a lightweight REST microservice.
- **Why this model?** The waitlist confirmation prediction is a tabular binary classification problem (Confirmed vs. Not Confirmed) with highly structured features (timestamps, numbers, categories). Traditional neural networks or LLMs (like GPT-4) are highly inefficient, expensive, and slow for this task. XGBoost offers extremely fast inference times (< 10ms), high accuracy on tabular data, and low computational overhead, allowing it to serve predictions smoothly even under heavy load or over 2G/3G connections.

---

## Training or Input Data
The model requires structured historical transaction data from the CRIS (Center for Railway Information Systems) database.

### Features required for training and inference:
1. **Train Number** (categorical identifier).
2. **Travel Class** (e.g., SL, 3A, 2A, 1A).
3. **Source & Destination Stations** (routing parameters).
4. **Journey Date & Time** (capturing seasonality, weekend travel spikes, holidays).
5. **Days before Departure** (when the ticket is being booked).
6. **Current Waitlist Status** (starting position and current position).
7. **Historical Chart Preparation Status** (the target label: 1 for Confirmed/RAC, 0 for Cancelled/Waitlisted).

### Data Source:
This historical data is stored inside railway database archives. The model will be trained offline on the last 5 years of booking records (anonymized) and updated weekly with recent trends.

---

## How Output Is Shown to the User
The probability indicator is rendered directly within the train search result card, next to the class status.

### UI Layout (ASCII representation)
```text
┌────────────────────────────────────────────────────────┐
│ 12622 TAMIL NADU EXPRESS | 22:00 SBC -> 06:00 MAS      │
├────────────────────────────────────────────────────────┤
│ [ Sleeper (SL) ]          [ AC 3 Tier (3A) ]           │
│   WL 14                     WL 5                       │
│  ┌──────────────────────┐  ┌────────────────────────┐  │
│  │ 🟢 82% Confirm Chance │  │ 🟡 64% Confirm Chance  │  │
│  └──────────────────────┘  └────────────────────────┘  │
│   Fare: ₹340                Fare: ₹840                 │
└────────────────────────────────────────────────────────┘
```
The color of the confirmation badge adjusts based on the prediction:
- **Green (High probability):** $\ge 75\%$
- **Orange (Medium probability):** $50\% - 74\%$
- **Red (Low probability):** $< 50\%$

---

## Confidence Threshold and Fallback
To ensure user trust, the system implements a strict confidence threshold and a fallback mechanism.

### Threshold Rules:
- **Prediction Confidence:** The XGBoost model outputs a probability score between `0.0` and `1.0`.
- **Hiding Low Confidence Predictions:** If the model's confidence in its classification is low (the output probability is close to the decision boundary, e.g., $45\% - 55\%$) or if the input features are incomplete, the system will **suppress the AI badge entirely**.

### Fallback Mode:
If the prediction service fails, experiences latency > 100ms, or returns an error, the UI falls back to the default IRCTC interface, showing only the raw waitlist numbers (e.g., "WL 14") without the badge. The failure degrades gracefully without blocking the search results from loading.

---

## Success Metrics
- **Booking Conversion:** Increase booking rate of high-probability waitlist tickets by 20% (since users are more confident to book).
- **Reduced Booking Churn:** 15% reduction in the number of duplicate backup tickets booked on different trains for the same journey.
- **System Accuracy:** The model maintains a minimum ROC-AUC score of `0.88` on the test validation set.

---

## Limitations and Risks
- **Model Inaccuracy (False Confirmations):** The model may predict an 85% confirmation chance, but unexpected events (such as sudden coach cancellations or a block booking by a tour group) might result in the ticket remaining waitlisted. Users might make non-refundable hotel bookings based on the AI confirmation prediction and feel misled.
- **Mitigation:** The UI displays a warning tooltip: *"This is a statistical estimate based on historical booking patterns and does not guarantee confirmation. Standard refund rules apply."*
- **Bias towards Historical Trends:** During major shifts in railway schedules, route extensions, or addition of new coaches, the historical training data will be temporarily out of sync, leading to inaccurate predictions until the model is retrained.
