
# What is Test Driven Development?

Test Driven Development (TDD) is when tests are written first before implementing the app code.

TDD is based on designing the system to do what **we want** it to do, rather than what the system already does do.

# The Testing Pyramid

Different tests can be written at different levels of the system. At each level, there is a balance between the speed of execution, the “cost” to maintain the test, and the confidence it brings to system correctness.

![[Pasted image 20240725160909.jpg]]

**Unit Tests:** A Unit Test exercises a small “unit” of the system that's isolated from the rest of the system. They should be simple and speedy. You want a high ratio of Unit Tests in your testing pyramid, as they’re key to designing highly cohesive, loosely coupled software.

**Integration Tests:** Integration Tests exercise a subset of the system and may exercise groups of units in one test. They are more complicated to write and maintain, and run slower than unit tests.

**End-to-End Tests:** An End-to-End Test exercises the system using the same interface that a user would, such as a web browser. While extremely thorough, End-to-End Tests can be very slow and fragile because they use simulated user interactions in potentially complicated UIs. Implement the smallest number of these tests.

# The Red, Green, Refactor Loop

 The Red, Green, Refactor development loop is a list of specific actions on how to write code, designed with the help of TDD cycles.
  
1. **Red:** Write a failing test for the desired functionality.
2. **Green:** Implement the simplest thing that can work to make the test pass.
3. **Refactor:** Look for opportunities to simplify, reduce duplication, or otherwise improve the code without changing any behavior—to _refactor._
4. Repeat!