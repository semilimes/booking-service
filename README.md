# Booking Service

## ✨ What's this about

Discover how quick and easy is to book visits, meeting rooms, sell event seats, rent cars, and so on... with no need for dedicated frameworks or custom development. Install, configure your environment, get paid for your services.

The **Booking Service** semilimes recipe takes advantage of the [semilimes](https://www.semilimes.com) platform services to setup any kind of bookable items over time, integrate your preferred payment service and book/sell your items. All on your mobile phones.

Get your free [semilimes app](https://www.semilimes.com) and start exploring this and other extension recipes!


## 🔎 What do you get

In this recipe, we offer an implementation of a medical visit booking, where the doctor sets up his available slots during each day, and patients can find and pay for any upcoming available appointments.

### Hosting options

You can choose to download this recipe and self-host it in any node-red environment. For a hassle-free approach, you can use the [semilimes Services](https://my.semilimes.net) to fully manage your recipe directly in a cloud-based environment. Choose the option that best fits for you.

### Centralized data

This recipe uses file-based databases hosted in the NodeRED environment, and you get configurable functions to setup the generation rules of the bookable items you need in your business. In this implementation rule, each selected day, from 9am to 3pm, is populated with slots of 10 minutes, with a pause of 5 minutes between each. 

### Administration forms

The administrator (doctor) is provided with an `slot activation` form to look for the slots to activate in each day.

### Stripe account integration

You don't need to operate on your payment service. Just connect your Stripe payment account and the automatic integration will create payment objects for each bookable slot that has been activated by the administrator.

### User forms

The users who access this system are provided with a `slot booking` form where only activated slots are proposed. The users can then directly reach the Stripe payment service to complete payment.

### In-app and email appointment reminders

Upon payment, automatic reminders are sent to both the administrator and the user in their semilimes app. Optionally, you can enable and configure your email service to send automatic email reminders to the user who booked the appointment.

### Free solution

You can use it as it is, tweak or extend it as much as you like. Everything is under your control!

## 📽️ How does it work

After this recipe is activated, the following will happen under the used subaccount...

### Internal Calendar creation

A new empty file-based calendar will be created under your NodeRED instance, ready to be populated with events

### Bookings Admin channel creation

The `Bookings Admin` channel is created and populated with a date selection/population form, meant to be used by the admin to select a general time span, and then use a calendar-like form to activate bookable slots, and/or to check if slots have been already activated or booked.

![Bookings Admin Channel Image 1](./images/bookingsAdminChannel_1.jpg)

![Bookings Admin Channel Image 2](./images/bookingsAdminChannel_2.jpg)

### Booking session private chat

In semilimes app, each user accessing this system will be placed in a private chat with this subaccount to privately process the date/slot selection and booking. Upon confirmation of a slot to book, a payment session will be proposed to the user, so that the payment can be finalized.

![Booking Chat Image 1](./images/bookingsChat_1.jpg)

![Booking Chat Image 2](./images/bookingsChat_2.jpg)

![Booking Chat Image 3](./images/bookingsChat_3.jpg)

### Booking Sessions internal channel creation

The `Booking Sessions` channel is an internal channel meant to receive each user selection during his booking session. This channel should not be shared with anyone since it may contain personal information.

![Booking Sessions Channel Image](./images/bookingSessionsChannel.jpg)

### Stripe integration

Upon (multi)selection and confirmation of `INACTIVE` slots in the `Bookings Admin` channel, the system will activate these slots and automatically create Stripe payment links for each of them, binding the relevant information in both directions:

- The booked slot will contain the link to the Stripe payment link
- Each Stripe payment link will contain metadata about the booked slot it is bound to, for data reconciliation.

Upon slot selection of a user, the Stripe payment link URL will be dynamically configured with a `client_reference_id` parameter holding the semilimes user account, so that Stripe knows who is actually going to pay for it.

Upon payment completion of a slot by a user on Stripe, the latter will send a webhook request to the booking system to make sure the slot is booked on behalf of the user that actually paid for it.

![Stripe integration Image](./images/slot_payment.jpg)

### Auto-reminders

Upon payment completion of a slot by a user, the booking system will send customized appointment messages to both the admin and the paying user. These appointment messages can be opened in the semilimes app and saved on the personal phone's agenda.

![Appointment reminder Image](./images/appointment_reminder.jpg)

If the email appointment feature is configured, the paying user gets an email with the same appointment reminder.

![Appointment email Image](./images/appointment_email.jpg)

## 👁‍🗨 Try it out!

We have already implemented a demo version to let you have a first look about how the process works.

In this case, you are a patient accessing the service to book a visit with your doctor.

| Link | QR Code |
| --- | --- |
| [Booking service Demo](https://cloud.semilimes.net/CloudServer/qr/5b9a563a-77a0-42fd-9b60-96ac4205d07d/0b508c3f-e69e-4ba6-aeb7-ad32597cd20d) | ![Demo QrCode Image](./images/demo-qrcode.jpg) |


Don't forget to explore *all demos* we have prepared for you!

| Link | QR Code |
| --- | --- |
| [All Demos](https://cloud.semilimes.net/CloudServer/qr/3857e37c-1cb8-462f-9ec8-0abdf63f23a3/6de7b9b0-6884-4b9f-984c-ce0ab92b0d9f) | ![NRDemos Image](./images/NRDemos-qrcode.jpg) |



## ⚙️ Configuration instructions

### Cloud-based solution

**Step 1: Create a NodeRED Instance**

- Access the [semilimes Services](https://my.semilimes.net)
- Select any of your subaccounts
- Setup API payment methods
- Enter the NodeRED section
- Select the instance type
- Select the NodeRED stack (**Note:** this recipe is tested with NodeRED 3.1.9)
- Open your freshly created NodeRED editor in the cloud

**Step 2: Create Webhook for Stripe**

- Access the [semilimes Services](https://my.semilimes.net)
- Select the same subaccount as before
- Enter the Webhooks section
- Select Create Webhook
- Choose a NodeRED service type
- Select the active NodeRED service from the combobox
- Enter `/stripe/confirmation` as the Path
- Activate it with the Active switch
- Confirm with OK

![Webhook Setup Image](./images/webhookSetup.jpg)

You should now have a new webhook. Take note of the webhook URL to be used in the next step.

![Webhook List Image](./images/webhookList.jpg)

**Step 3: Configure your Stripe account**

- Open and access your [Stripe](https://stripe.com) account.
- Open the developers page
- Enter the `API keys` pane
- Select Create restricted key

    ![Stripe Api Key List](./images/stripeApiKeyList.jpg)

- Give it a name and the following permissions to the restricted key
  - `Products: Write`
  - `Checkout Sessions: Read`
  - `Prices: Write`
  - `Webhook Endpoints: Write`
  - `Payment Links: Write`

**Step 4: Verify Environment Variables**

You should check you have the following environment variables already set-up for you for this recipe to work:

- `SME_API_KEY`: for using semilimes API within NodeRED
- `STRIPE_KEY`: enter the restricted key you created in Step 3
- `STRIPE_WEBHOOK`: enter the webhook URL you created in Step 2

![NR Env Variables Image](./images/nodeRedEnvVariables.jpg)

**Step 5: Download necessary NodeRED packages**

In your NodeRED editor, select "Manage Palette" in the menu, and install the following packages:

- `@semilimes/node-red-semilimes`

**Step 6: Import the recipe**

- Download the three flows:
  - [BookingCalendar](https://github.com/semilimes/booking-service/blob/main/node-red/bookingCalendar_flow.json)
  - [BookingAdmin](https://github.com/semilimes/booking-service/blob/main/node-red/bookingAdmin_flow.json)
  - [BookingRequest](https://github.com/semilimes/booking-service/blob/main/node-red/bookingRequest_flow.json)

- In NodeRED, choose Import from the menu, and select the files you downloaded, importing one by one
- In the right panel, select the wheel icon to select the connector nodes. Double click on "SMECON" semilimes connector and verify that the API KEY selected is set to `SME_API_KEY` or whatever you configured in Step 3 as an environment variable. If the field is empty, make sure you fill it before proceeding.

![SmeConnector Config](./images/smeConnectorConfig.jpg)

- Click Deploy

You should now have your recipe ready for startup!

**Step 7: Activate the recipe**

In the `BookingCalendar` flow:

- Open `Set Slot Generation Config` and setup your preferred slot generation rules:
  - `startTime` is the time of the day to start considering your overall availability
  - `endTime` is the time of the day to end your overall availability
  - `slotDuration` is the duration of each bookable slot
  - `pauseDuration` is the time distance between the end of a slot and the beginning of the next one
  - `timezone` is the timezone in which the exact times will be generated. Explore all the [timezone identifiers](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

![Booking Calendar Config Image](./images/bookingCalendarFlowCalendarConfig.jpg)

- Open `Create Product Payload` under the StripeAPI: Create Payment Links group.
  - change `default_price_data[currency]` to the currency of your choice
  - change `default_price_data[unit_amount]` to the price of your choice. Make sure you enter the number as cents (so 50.00 EUR is represented with the number 5000)

![Booking Calendar Stripe Price Image](./images/bookingCalendarFlowStripePriceConfig.jpg)

- Trigger `Reset Calendar`: this will create an empty calendar ready to use, and the `Bookings` channel

![BookingCalendar Flow Setup Image](./images/bookingCalendarFlowSetup.jpg)

- Trigger `Create Webhook` (ONCE ONLY!) to call Stripe and create a webhook connection Stripe->semilimes



In the `Bookings Admin` flow:
- Trigger `Setup`: this will create the `Bookings Admin` channel and the date selection form message for the administrator

![BookingAdmin Flow Setup Image](./images/bookingAdminFlowSetup.jpg)

In the `Booking Requests` flow:
- Trigger `Setup`: this will create the `Booking Sessions` channel and the date selection form message for the users

![Booking Requests Flow Setup Image](./images/bookingRequestsFlowSetup.jpg)

**Step 8: Verify channels on your semilimes app**

Access your semilimes app, select your subaccount and verify that the required channels have been created

**Step 9: Create App Entry Point**

In your semilimes app, under the same subaccount used with NodeRED, create a `Form Template` with:

- `Receiver` set to the `Booking Sessions` channel
- `Reference` set to `startBookingSessionForm`
- Form content set to whatever you like, with a submit button

![Template Start Booking Session Image](./images/templateStartBookingSession.jpg)

Place this template to wherever you like within your subaccount (e.g. in a tile or a group chat).

Each user submitting this form will receive a private message from your subaccount so he can start a private booking session.


## 💡Extension ideas 

...

## 🛠️ Troubleshooting 

In Bookings Admin flow, you can find and enable "service" flows for working with Stripe APIs.
For example, you can clear your Stripe environment by deleting all payment links, products and prices.

![Test API flows Image](./images/test_api_flows.jpg)

## Changelog

### v1.1.0

1. Updated @semilimes/node-red-semilimes package dependency to version 1.2.1
2. Replaced the multiple choice form for activating slots and selecting available slots with the new Event Picker for showing events over a fixed timespan in a calendar-shaped form
3. Added configuration helpers in the calendar flow and documented how to configure slot generation parameters and stripe product prices
4. Now retrieving the provided name and email from the Stripe session to fill the appointment reminders and emails
5. Implemented optional email sending feature for booked appointments
6. Updated Stripe API calls to create one-time-only payment links, so that each specific link will be automatically deactivated after the first completed payment
7. Updated service Stripe APIs to clear the Stripe environment
8. Moved the STRIPE_KEY environment variable reading to function nodes prior to the Stripe HTTP call nodes
9. Added this changelog

### v1.0.0

Initial Release
