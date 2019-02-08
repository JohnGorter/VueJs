# Lab 2: Props, State, Lifecycle & Events
In this series of labs, we'll build a simple web application that shows you some information about a car with a Dutch licence plate.

## Step 1: Implementation of an input container
Let's assume that in step 3 of the previous lab, you came up with some kind of a container element for collecting input.
We can now implement this container.

1. Basic requirement: keep the licence plate number that the user has entered.
We can use the state of a React component for this.
1. Additional, nice-to-have: validate whether the input is a valid licence plate number.
You can use a regular expression like `([A-Za-z0-9]){6}|([A-Za-z0-9\-]){8}`.
   * Before implementing this, think of how you will keep track of the user input validity.
   * If the user enters a _valid_ licence plate number, highlight the input element with a green border.
   * If the user enters an _invalid_ licence plate number, highlight the input element with a red border.
