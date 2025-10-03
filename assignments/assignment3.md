# Assignment 3
Gloria Pulido

[ReadMe](../README.md)
## 1. Augment the design of a concept.

### Original 
> concept Milestones [User, Hobby]\
> purpose allow users to monitor the progress that they are making toward their goals\
> principle after a user inputs their goals, they will see a set of steps they are yet to complete and those that they have completed\
> state\
> a set of goals Strings\
> a set of steps Strings with
>> a description String\
>> a start Date
>> a completion Date\
>> a status String
>> an activity status Boolean

> actions\
> addGoal (goal: String): (goals: Strings)
>> requires goal is not already in set of goals\
>> effects adds goal to set of goals

> startStep (step: String): (steps: Strings)
>> requires step has a not started status\
>> effects marks step as a status in process, records start date

> completeStep (step: String): (steps: Strings)
>> requires step has an in process status\
>> effects marks step as a status complete, records completion date\
>> if all steps are complete, mark Milestones as inactive

> closeMilestones ()
>> requires Milestones to be active\
>> effects marks Milestones as inactive

### Updated
