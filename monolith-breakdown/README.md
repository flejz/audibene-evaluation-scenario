# Monolith Breakdown

## Mapping entities

* Account
* Appointment
* Device

## Required interfaces

Given the provided information, below Entities are being queried and managed by the monolith.

Case 1:

* Appointment, Customer and Fitter information fetch

Case 2:

* Appointment, Customer, Fitter and Device information fetch
* Appointment and Device management

## Data flow scenarios

Given the above cases, following data flows can be considered.

### Apointment scheduling
* Customer schedule an Appointment with Fitter.
* **Logistics shipment to customer** gets triggered
* **Appointment update** gets triggered

### Appointment update
* **Notify Customer about Appointment update** gets triggered for Appointment
* **Notify Fitter about Appointment update** gets triggered for Appointment

### Appointment matching
* A job matches recurring Appointments between Customer and Fitter.
* The job schedule an Appointment between Customer and Fitter.
* **Logistics shipment to customer** gets triggered
* **Appointment update** gets triggered

### Appointment ended
* Fitter informs that Appointment ended.
* **Logistics device retrieval** gets triggered

### Customer sends message to Fitter
* **Notify Fitter about Appointment update** gets triggered for Appointment

### Fitter sends message to Customer
* **Notify Customer about Appointment update** gets triggered for Appointment

### Logistics shipment to Customer
* Logistics creates a shipment order of a Device to get shipped to Customer as a result of the Appointment.
* Partner collects Device, Logistics updates shipment order with tracking info and status.
* Logistics listen to/reads from shipment status changes and updates shipment order status.
* **Appointment update** gets triggered once shipment order status is "delivered".

### Logistics Device retrieval
* Logistics creates a shipment order of a Device to get colleted from Customer as a result of the Appointment.
* **Notify Customer about Appointment update** gets triggered for Appointment
* Partner collects Device, Logistics updates shipment order with tracking info and status.
* Logistics listen to/reads from shipment status changes and updates shipment order status.
* **Appointment update** gets triggered when Logistics registers Device retrieval and close ship order.

### Notify Customer about Appointment update
* Notify Customer about Appointment update

### Notify Fitter about Appointment update
* Notify Fitter about Appointment update

Some not covered flows:
* Appointment rescheduling from Customer or Fitter
* Appointment rescheduling due to Device malfunction
* Appointment approval for Customer and Fitter
* Device lost on delivery or retrieval
* Device delivery outdated for Appointment
* Manual device assignment for an Appointment
* Customer, Fitter and Device availability os Appointment scheduling
* Reminder about appointment
* Among many...

## Responsibility identify and segregation

Some high level domains responsible for providing and managing information were identified and subsequentially tough as services.

Entity Services

* Account Service
* Appointment Service
* Device Service

Support Services

* Logistics Service
* Notification Service
* Chat Service

Where:

* **Account Service** will hold Customer and Fitter operations (as defined in the Account Service challenge)
* **Appointment Service** will be responsible for managing Appointments, dependant on Account and Device Services,
* **Device Service** will hold Device information. Will be dependant on Logistics services to acquire current statuses.
* **Logistics Service** will be responsible for the operations regarding Devices and Appointments.
* **Notification Service** will broadcast notifications on certain ecosystem events. Websocket tunnels as well as any other kind of notification (SMS, E-mail, Push notication) will be handled here.

### SRD

![SRD](images/srd.png)

## ERD:

![ERD](images/erd.png)

## Migration plan
