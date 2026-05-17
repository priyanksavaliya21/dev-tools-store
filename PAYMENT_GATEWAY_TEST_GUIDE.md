# Stripe Payment Gateway Testing Guide

**🔴 Live Test Environment:** [https://dev-tools-store.vercel.app/](https://dev-tools-store.vercel.app/)

This guide provides comprehensive test cases and card details to verify the Stripe payment flow in the application. Ensure your application is using Stripe **Test Mode** API keys before attempting these scenarios. Never use real card details in test mode.

## 1. General Payment Flow Test (Successful Payments)

**Goal:** Verify that a standard payment can be successfully processed.

When testing interactively, enter the card number in any payment form.
- **Expiration Date:** Any valid future date (e.g., `12/34`)
- **CVC/CVV:** Any 3 digits (e.g., `123`)
- **ZIP/Postal Code:** Any valid code (e.g., `10001`)

**Standard Success Card (Visa):**
- **Card Number:** `4242 4242 4242 4242`
- **Expected Result:** Payment succeeds, redirects to the order confirmation page.

## 2. Card Error Test Cases (Declined Scenarios)

Use the following specific card numbers to trigger different types of card errors and declined payments. Use any valid future expiration date and any 3-digit CVC.

| Scenario | Card Number | Expected Result |
| :--- | :--- | :--- |
| **Generic Decline** | `4000 0000 0000 0002` | Payment is declined (`card_declined`). UI shows a generic decline message. |
| **Insufficient Funds** | `4000 0000 0000 0004` | Payment is declined (`insufficient_funds`). UI shows insufficient funds message. |
| **Lost Card** | `4000 0000 0000 0005` | Payment is declined (`lost_card`). UI shows card reported lost. |
| **Stolen Card** | `4000 0000 0000 0006` | Payment is declined (`stolen_card`). UI shows card reported stolen. |
| **Processing Error** | `4000 0000 0000 0011` | Payment fails due to `processing_error`. UI asks user to try again later. |
| **Incorrect CVC Decline** | `4000 0000 0000 0127` | Payment is declined (`incorrect_cvc`) if any CVC is provided. |
| **Expired Card** | `4000 0000 0000 0003` | Payment is declined (`expired_card`). |

## 3. Trigger Errors with Invalid Data

To test errors resulting from invalid data, you don't need a special test card. Provide invalid details to trigger validation errors:

- **Invalid Expiry Month (`invalid_expiry_month`):** Use an invalid month, such as `13`.
- **Invalid Expiry Year (`invalid_expiry_year`):** Use a year up to 50 years in the past, such as `95`.
- **Invalid CVC (`invalid_cvc`):** Use a two-digit number, such as `99`.
- **Incorrect Number (`incorrect_number`):** Use a card number that fails the Luhn check, such as `4242 4242 4242 4241`.

## 4. Fraud Prevention (Radar) Testing

Stripe Radar can block payments when they have a high risk level. Use these specific cards (with any future date and valid CVC/Postal code) to test how your application handles fraudulent payments blocked by Radar:

| Scenario / Risk Level | Card Number | Expected Result |
| :--- | :--- | :--- |
| **Always Blocked (Highest Risk)** | `4100 0000 0000 0019` | Radar *always* blocks it. Error code is `fraud`. |
| **Highest Risk (Settings Dependent)** | `4000 0000 0000 4954` | Radar might block depending on your settings. |
| **Elevated Risk** | `4000 0000 0000 9235` | Radar might queue for manual review. |
| **High Fraud Dispute Score** | `4000 0084 0000 0407` | Simulates a high dispute risk. |
| **CVC Check Fails** | Provide any standard test card (e.g., `4242...`) but enter an invalid CVC (like `127`). | Radar might block depending on traditional bank check rules. |

## 5. 3D Secure Authentication Testing

To simulate payment flows that require 3D Secure (3DS) multi-factor authentication (critical for SCA in Europe), use these specific test cards. They allow you to test if your application properly handles redirects to bank authentication pages.

| Scenario | Card Number | Expected Result |
| :--- | :--- | :--- |
| **3DS Required (Auth Succeeds)** | `4000 0027 6000 3184` | Triggers a 3DS challenge. If you complete it, the payment succeeds. |
| **3DS Required (Auth Fails)** | `4000 0084 2000 1629` | Triggers a 3DS challenge. It automatically simulates an authentication failure. |

## 6. Dispute Simulation (Chargebacks)

To simulate a disputed transaction (chargeback) in test mode, specific cards can be used. This is extremely useful for testing automated dispute handling and webhooks.

| Scenario | Card Number | Expected Result |
| :--- | :--- | :--- |
| **Fraudulent Dispute Created** | `4000 0000 0000 8014` | Payment succeeds initially, but a dispute (reason: fraudulent) is immediately created. |

**Webhook Testing for Disputes:**
- Verify that your backend correctly receives the `charge.dispute.created` webhook when the card above is used.
- Test submitting evidence (winning or losing) through the Stripe Dashboard to see how your application handles the final dispute resolution.

## 7. Interview Demo Flow & Script

If you are demonstrating this payment gateway integration in an interview, follow this structured flow to showcase your robust implementation and attention to detail:

**Step 1: The Happy Path (Success Demo)**
- **What to say:** "First, I'll walk you through a standard, successful checkout flow to show the core integration."
- **Action:** Add an item to the cart and use the standard `4242 4242 4242 4242` Visa card.
- **Highlight:** Point out the smooth UI, the lack of page reloads (if using Stripe Elements), and the success redirection.
- **Backend touch:** Quickly show the Stripe Dashboard (in Test Mode) or your database to prove the order was recorded and the charge succeeded.

**Step 2: Client-Side Validation (The UX Demo)**
- **What to say:** "Before a request even hits our server or Stripe, I've implemented client-side validation to catch common user errors immediately, providing a better user experience."
- **Action:** Try to submit the form with an empty card, a short card number (e.g., `4242`), or an invalid CVC format (e.g., `99`).
- **Highlight:** Show how the input fields automatically highlight the exact error with an inline message, preventing a wasted network request.

**Step 3: Server-Side Error Handling (The Edge Case Demo)**
- **What to say:** "Of course, even with perfect inputs, cards can be declined by the bank. I want to show how the application gracefully handles these backend edge cases without crashing."
- **Action:** Enter the **Insufficient Funds** card (`4000 0000 0000 0004`).
- **Highlight:** Show the user-friendly error message rendered on the screen. Mention that your backend specifically catches the Stripe API error code and maps it to a safe, readable message for the frontend.

**Step 4: Advanced Scenarios (Bonus Points)**
- **What to say:** "For complete robustness, the system is also prepared for more complex flows."
- **Discussion Points:** Briefly mention how your app uses Webhooks to verify payments securely in the background, or how it supports 3D Secure (SCA) if a card requires multi-factor authentication.

---

## Testing Checklist

- [ ] Successful payment redirects to the success page and records the order.
- [ ] Generic decline shows an appropriate error message to the user.
- [ ] Insufficient funds shows an appropriate error message.
- [ ] Form validation prevents submission for invalid CVC, expiry, and Luhn-failing card numbers.
- [ ] 3D Secure redirect flows complete successfully and return the user to the correct page.
- [ ] Webhooks for successful payments and failed payments are correctly received and processed.
- [ ] No actual money is charged (verified in Stripe Test Dashboard).
