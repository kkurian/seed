# Application Domain Model

> This file holds the domain model for the application being built with the seed. Unlike `domain-model.md` (which defines the immutable process), this file evolves as the application is specified, derived, and refined. Every change must be classified (Correction, Defect, Discovery, Evolution) and approved by the human before it lands.
>
> The application is seeded with a minimal Hello World. Replace or extend it as the application's real domain emerges.

## Bounded Context: Greeting

### Ubiquitous Language

#### Greeting
A textual message the program emits to acknowledge a Recipient.

#### Recipient
The party a Greeting is addressed to. In the seeded Hello World, the Recipient is the world.

### Relationships

- A **Greeting** is addressed to a **Recipient**.
