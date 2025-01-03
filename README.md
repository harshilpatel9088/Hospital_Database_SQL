# Hospital Database System

## Overview

This project aims to design and implement a comprehensive hospital database system to manage healthcare operations efficiently. The database comprises 12 interconnected tables to handle information on patients, doctors, appointments, billing, medical records, prescriptions, rooms, and staff. Advanced techniques, such as normalization (up to 3NF), constraints, triggers, and stored procedures, were used to ensure data integrity and efficiency. Analytical queries and reports provide insights into revenue, patient trends, staff workload, and room occupancy, supporting effective hospital management.

## Features

1. **Data Integrity**  
   Ensuring data accuracy and consistency is paramount. The following measures were implemented:
   - **Normalization**: Applied up to Third Normal Form (3NF) to eliminate redundancy and improve data integrity.
   - **Constraints**: Enforced primary keys, foreign keys, and unique constraints to maintain data accuracy.
   - **Triggers**: Created triggers for automatic enforcement of business rules and consistency.
   - **Stored Procedures**: Developed procedures for routine operations to streamline tasks and maintain data integrity.
   - **Views**: Created views to simplify complex queries and provide consistent data representations.

2. **Interconnected Tables**  
   The database includes 12 interconnected tables to manage various aspects of hospital operations:
   - **Patients**: Stores patient information.
   - **Doctors**: Stores doctor information.
   - **Appointments**: Manages appointments between patients and doctors.
   - **Billing**: Handles billing details.
   - **Medical Records**: Maintains patient medical records.
   - **Prescriptions**: Tracks prescriptions issued to patients.
   - **Rooms**: Manages hospital room information.
   - **Staff**: Stores staff information.

3. **Analytical Insights**  
   The system provides various analytical queries and reports:
   - **Revenue Reports**: Analyze hospital revenue from billing data.
   - **Patient Trends**: Track patient trends over time.
   - **Staff Workload**: Monitor staff workload and efficiency.
   - **Room Occupancy**: Report on hospital room occupancy rates.

4. **PowerBI Dashboard**  
   An interactive PowerBI dashboard was created to visualize and analyze the hospital data effectively. The dashboard provides insights into key metrics such as:
   - Revenue and profit trends.
   - Patient demographics and trends.
   - Staff workload and performance.
   - Room occupancy rates.
   - Medical records and prescription trends.
  
## Methodology

### Tables
The database consists of 12 tables, designed to reduce data redundancy and increase data integrity. Most of the tables have been normalized to Third Normal Form (3NF), ensuring there are no partial or transitive dependencies. Each table has a well-defined primary key to maintain uniqueness and support efficient data management. Additionally, several constraints, such as foreign keys, unique constraints, and check constraints, have been applied to enforce data integrity and ensure consistent relationships between tables.

1. **Patient Table**: This table contains information about patient data.
   ![Image 1](path/to/image1.png)  
   Here is a DDL (Data Definition Language) for the patient table showcasing the constraints for the other required columns.

   There has been a stored procedure created to simplify adding patients to the table.

   Example:  
   ![Image 2](path/to/image2.png)  
   The stored procedure generates a unique patient ID every time a patient record is added using the stored procedure.

2. **Department Table**: This table contains information about different departments in the hospital with DepartmentID as the primary key.
   ![Image 3](path/to/image3.png)

3. **Doctor Table**: Contains information about doctors with DoctorID as the primary key and DepartmentID as the foreign key referencing the department table. This setup ensures that each doctor is associated with a specific department. For example, a doctor belonging to the radiology department will be considered a radiology specialist. This approach reduces data redundancy by maintaining department names in a single table.
   ![Image 4](path/to/image4.png)

4. **Doctor Availability Table**: This table stores information about doctor availability to effectively manage appointment bookings and cancellations. The Availability ID is the primary key, also referred to as the Slot ID, which helps track whether a specific slot for the doctor is available. The Is Available column indicates whether the slot is booked or still available.
   ![Image 5](path/to/image5.png)

   Example output for the table:
   ![Image 6](path/to/image6.png)

5. **Appointment Table**: This table tracks appointments booked for patients and includes the AvailabilityID from the Doctor Availability table. This helps to track appointments for each patient and provides information about the doctor the patient is meeting with. 
   ![Image 7](path/to/image7.png)

   - **PatientID Foreign Key Constraint**: Ensures that only existing patients can book appointments. Patient ID is not unique, allowing patients to book multiple appointments.
   - **AvailabilityID Constraint**: Not set to unique, allowing flexibility in rebooking canceled appointments. A trigger checks the availability of the slot in the Doctor Availability table before booking an appointment. If the slot is available, it changes the IsAvailable column to "Not Available" upon successful booking. If the appointment is canceled, the trigger updates IsAvailable back to "Available," allowing the slot to be reused.

   **Triggers for Appointment Booking**:
   ![Image 8](path/to/image8.png)
   ![Image 9](path/to/image9.png)
   ![Image 10](path/to/image10.png)

   Functionalities of the trigger:
   - Check if the slots being booked are available. If not, rollback to the previous state.
   - Ensure the date for the slot is not in the past. If it is, rollback.
   - Appointments can have three stages: Scheduled, Cancelled, and Completed. At the time of booking, the status can only be "Scheduled."
   - Automatically generate a bill with a fixed amount of $100 and set the status to "Unpaid."
   - Update the IsAvailable column to "Not Available" once the appointment is successfully booked.

   **Stored Procedure**:
   A stored procedure has been created for booking appointments, which generates an AppointmentID automatically.

   **Test Case**:
   ![Image 11](path/to/image11.png)  
   Availability ID 6324 is not available. Attempting to book an appointment for this ID throws an error.

   ![Image 12](path/to/image12.png)  
   Availability ID 6325 is available. Booking this slot for Patient ID P5 is successful, and the IsAvailable column is updated to "Not Available." The stored procedure generates a unique Appointment ID APNT4727, and a billing record is added.

   **Appointment Cancellation**:
   ![Image 13](path/to/image13.png)  
   If the appointment is canceled, AvailabilityID 6325 is updated back to "Available," allowing future bookings. The billing record is deleted, ensuring data consistency and no redundant data.

6. **Billing Table**: This table tracks the billing information for the appointments booked.
   ![Image 14](path/to/image14.png)
   
   The Billing table has BillingID as the primary key. There is no "not null" constraint on the date and payment method because there can be unpaid bills in the database.
   
   Bills are tracked by appointments instead of patient ID to reduce complexity in the database. There can be both paid and unpaid bills for each patient. Since bills are generated with appointments, multiple joins would be needed to find the bills for each patient. To overcome this, a stored procedure has been created that retrieves all the bills for a patient present in the database.
   ![Image 15](path/to/image15.png)
   
   `EXEC PatientBills` retrieves the bills for the patient.
   
   Now, we have the bills for each patient. If the patient decides to pay the bill, a stored procedure can be used as shown below.
   ![Image 16](path/to/image16.png)
   
   Example:  
   ![Image 17](path/to/image17.png)
   
   This approach for the Billing table helps maintain data integrity and reduces data redundancy.
