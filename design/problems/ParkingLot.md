# Design a Parking Lot

- [Design a Parking Lot](#design-a-parking-lot)
  - [Parking Lot System — Detailed Problem Statement](#parking-lot-system--detailed-problem-statement)


## Parking Lot System — Detailed Problem Statement

Design an object-oriented system to manage a multi-level parking lot. The system should support parking and unparking vehicles, tracking available spaces, and calculating parking fees. The design should be modular, extensible, and follow good OO design principles.

The parking lot may contain multiple floors, and each floor may contain different types of parking spots (for example: motorcycle, compact, large).

🎯 Core Goals

The system should support:

Assigning an available parking spot to a vehicle when it enters

Ensuring vehicles can only park in compatible spot types

Tracking which spot a vehicle is parked in

Handling vehicle exit and freeing the spot

Calculating parking fees

Parking Spot Types & Rules

The lot supports multiple spot types:

Spot Type	Can Park Vehicles

Motorcycle	Motorcycle only
Compact	Motorcycle, Car
Large	Motorcycle, Car, Truck/Bus

If no suitable spot is available, the system should indicate the lot is full for that vehicle type.

Vehicle Types

The system should support at minimum:

Motorcycle

Car / Sedan / SUV

Truck / Bus

Each vehicle has

license plate

type

entry timestamp

Parking Structure

The parking lot consists of:

Multiple Levels/Floors

Each Level contains:

N motorcycle spots

N compact spots

N large spots

A level may become full even if other levels have space.

Parking Process Behavior

When a vehicle arrives:

System checks for the first available suitable parking spot

Spot is reserved and marked occupied

Vehicle gets assigned a parking ticket

Entry time is recorded

If no valid spot exists, the vehicle is rejected.

When a vehicle exits:

Ticket is validated

Fee is calculated

Spot is marked available again

⏳ Parking Fee Rules (Basic Version)

You may assume:

First 30 minutes → free

After that → hourly rate (rounded up)

Different vehicle types may have different pricing tiers

The design should make pricing extensible (e.g., support coupons, peak pricing).

🎛 Operational Requirements

Support queries such as:

Get number of available spots by type

Get number of available spots per level

Check whether lot is full

Find where a vehicle is parked (given license plate)

⚙️ Non-Functional / Design Constraints

Your design should emphasize:

Encapsulation

Clean abstractions

Extensibility for new features (EV charging, reservations, valet, etc.)

Avoiding tightly-coupled logic

Prefer composition over inheritance where appropriate.

🚫 Things You Do NOT Need to Implement

To keep the interview scope manageable, assume:

No real payment processing

No gate automation hardware

No security and fraud prevention

No concurrency or distributed architecture (unless interviewer asks)

Focus on OO modeling and interactions.

👍 Optional Extensions (for deeper discussion)

The interviewer may later ask you to extend the system for:

EV charging spots

Handicapped spots

Parking reservations

Monthly subscription passes

Dynamic pricing

Camera-based license recognition

Support for concurrent entrances/exits

Multi-lot management

Your design should be flexible enough to evolve.
