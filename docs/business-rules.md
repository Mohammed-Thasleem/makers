---
title: "Business rules"
purpose: "Canonical Phase 1 product rules"
audience: agent
---

# Business Rules

## Scope

- Phase 1 is a Kerala marketplace for discovering creative professionals and sending a structured inquiry. It is not a checkout, chat, or calendar-booking product.
- Surfaces: customer mobile app (iOS and Android) and a web Vendor Dashboard. Admin data entry is an internal MAKERS process.
- Tagline and product name stay **MAKERS** / "Find the people who make it happen".
- Phase 1 money is **INR**. There is no currency switcher. The PRD does not define paise storage or tax.

## Geography

- Phase 1 operates **only in Kerala, India**. Vendors, search results, and the district selector do not include locations outside Kerala.
- Discovery is **district-scoped**. After login the app asks for location so results match the customer's district.
- If location permission is denied, the customer must pick a Kerala district before vendor results are shown.
- The customer can change district from the home location selector. Results, the Daily Services strip, and planner recommendations follow the selected district.
- Each vendor belongs to **one district**, set by the MAKERS admin team.

## Authentication

- Primary sign-in is **phone OTP**. Secondary sign-in is **Continue with Google**.
- The location prompt happens immediately after a successful login, before district-scoped discovery.
- **Open:** the PRD does not say whether phone and Google create one account, or what profile fields exist beyond the profile icon.

## Home discovery

- The top bar shows the MAKERS logo, the district selector, the profile icon, and a 3-dot menu.
- **Host Your Business** is the first item in the 3-dot menu and is visually highlighted.
- Below that: a vendor search field, then two primary actions. **Plan Event** and **AI Event Manager** are separate tools. They are not two entries to the same planner.
- **AI Event Manager** is the AI Budget Planner specified below. **Plan Event** has no step-by-step behavior in the Phase 1 PRD.
- Category chips scroll horizontally and filter discovery. Promotional banners rotate. A **Daily Services** strip highlights salons and spas for repeat weekly use.
- **Open:** search matches (name, category, district) are not defined. **Open:** spas are named in the strip, but the category list has only Men's / Women's Salon.

## Service categories

| Category | Phase 1 | Customer can inquire |
|---|---|---|
| Event Management | Live | Yes |
| Catering Services | Live | Yes |
| Photographers | Live | Yes |
| Makeup Artists | Live | Yes |
| Men's / Women's Salon | Live | Yes. This category feeds the Daily Services strip. |
| Exotic Automobiles | Coming Soon | No. Shown in the UI with a Coming Soon tag. |
| Mehndi Artists | Coming Soon | No. Shown in the UI with a Coming Soon tag. |

- A Coming Soon category stays visible and tagged. It does not open a vendor list, a profile inquiry, or the booking handoff.
- No other category is in Phase 1.

## AI Budget Planner

- The **AI Event Manager** home action opens this planner. Required inputs: event type, guest count, total budget, district, and priority focus (for example food-first or venue-first).
- Output is a **percentage split of the total budget** with a **rupee amount per category**, plus recommendation chips. Each chip opens vendors on the platform for that category in the selected district.
- Chips may link only to **Live** categories that have vendors in that district.
- Similar queries must be served from cache so repeat plans do not call the model again.
- The planner does not create a Booking ID and does not start a WhatsApp inquiry.
- **Open:** what counts as a "similar" query (exact normalized inputs, or a tolerance) is not defined.

## Vendor profile

- A public profile shows a vendor-uploaded cover photo, an avatar, business name, service category, district, and an About of **2–3 lines**.
- Social proof is a star rating with a review count, verified customer reviews, and a grid of work samples.
- The MAKERS team applies a **verification badge** only after it has verified the vendor. The badge is not self-claimed.
- An unverified vendor stays **visible** in search and on the public profile, without the badge.
- Admin, not the vendor, enters the basic record, uploads portfolio photos, and sets the **starting price range**.
- **Open:** who can write a review, and whether a review requires a Completed job. **Open:** whether the starting price range is shown on the profile and used as a filter.

## Inquiry and WhatsApp handoff

Phase 1 does not take payment and does not lock a date. The customer action is an **inquiry**. Every submitted inquiry gets a unique **Booking ID** and this payload:

| Field | Rule |
|---|---|
| Event type | Type of event being planned |
| Event date | Date of the event |
| Guest count | Expected number of guests |
| Location | Event location / district |
| Budget range | Customer's budget range |
| Special notes | Optional customer notes |
| Booking ID | Generated by the backend for tracking. Required on every request. |

- The profile's primary action is WhatsApp, not an in-app message thread and not a pay button.
- Both paths run on submit:
  - The customer's WhatsApp thread is with **MAKERS**. MAKERS contacts the vendor the customer selected.
  - The same structured request is sent to the **vendor's WhatsApp Business account**, and a **Vendor Dashboard** alert is created for that vendor.
- The vendor alert is in the dashboard only. Mobile push delivery is excluded in Phase 1.

## Vendor onboarding

- There is **no self-serve vendor onboarding** and **no vendor profile editing** in Phase 1.
- MAKERS admin manually creates the vendor, uploads portfolio photos, and sets the starting price range.
- **Host Your Business** does not open a vendor editor or a signup dashboard. It captures a lead for the admin team: who to contact, which category, and which district.

## SLA

- A new request must be answered within **4 hours**. A valid answer is **Accept**, **Decline**, or **a clarifying question**.
- The Vendor Dashboard shows a countdown for each pending request.
- A reminder is sent **30 minutes** before the 4-hour window closes. A missed SLA lowers that vendor's visibility and ranking.
- A clarifying question sent inside the window **stops the clock** and counts as the vendor's response. The request is not a missed SLA. It stays open: it does not become Confirmed and it is not Declined.
- **Open:** how much ranking or visibility drops is not set. **Open:** how the customer hears an accept or decline, given no in-app chat and no push.

## Availability

- Vendors block dates manually on a calendar to avoid taking conflicting work.
- A blocked date is advisory for the vendor during Accept / Decline. It does not auto-book, auto-reject, or lock that date for the customer, and it does not remove the vendor from search.
- Automated calendar booking is out of scope.

## Jobs and recorded payments

- **Accept** moves the request to **Confirmed**. **Decline** ends the request and does not create a job. A clarifying question does not create a job.
- After acceptance, the vendor updates job status by hand through: **Confirmed → Preparation → In Progress → Completed**.
- There is no in-app payment. The vendor logs **advance** and **balance** against the Booking ID, each as **Pending** or **Received**.
- **Open:** whether status can move backward, and whether a cancelled state exists. The PRD lists only the four statuses above.

## Vendor analytics

- The dashboard shows conversion (requests received vs converted), average response time, upcoming events for the **next 7 to 30 days**, and an earnings summary.
- A request counts as **converted** only when its job status is **Completed**. Accepted or Confirmed requests are not conversions.
- Earnings are the sum of amounts the vendor marked **Received**. MAKERS does not collect them.

## Phase 1 exclusions

Do not build these in Phase 1:

| Excluded | Rule |
|---|---|
| Payment gateways | No Razorpay and no other in-app payment capture. |
| In-app chat | All conversation is handed off to WhatsApp. |
| Automated calendars | No date-based booking flow and no automatic date lock. |
| Self-serve vendor tools | No vendor onboarding and no vendor profile editing. |
| Sub-event flows | No guided split of one event into separate searches (for example Mehndi and Reception). |
| Premium tools | No Vendor Event Manager task tracker, no push notification delivery, no UTSAV loyalty coins, no guest management. |
