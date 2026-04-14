# Application Specification

> This file holds the behavioral specification for the application being built with the seed. Unlike `specification.md` (which defines the immutable process), this file evolves as the application's intended behavior is described and refined. Every change must be classified (Correction, Defect, Discovery, Evolution) and approved by the human before it lands.
>
> The application is seeded with a minimal Hello World. Replace or extend it as the application's real behavior emerges.

## Bounded Context: Greeting

### Emit Greeting to the World

When the program runs,
then the program emits the text `Hello, world!` followed by a newline to standard output,
and the program exits with status 0.
