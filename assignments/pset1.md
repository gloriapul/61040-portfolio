# Problem Set 1
Gloria Pulido

[ReadMe](../README.md)

## Exercise 1

### 1. What are two invariants of the state? (Hint: one is about aggregation/counts of items, and one relates requests and purchases). Say which one is more important and why; identify the action whose design is most affected by it, and say how it preserves it.

One invariant would be that the amount of an Item attempting to be bought in a Purchase must be less than or equal to the amount of the Item still being requested in the Request. The action whose design would most be affected by this invariant would be purchase. This action preserves the invariant by checking that for the amount of an Item attempting to be purchased, there must exist at least that count for the Item in the Request. This ensures that the count of the Item in a Request does not become negative and not more than what is needed is purchased. 

Another invariant would be that every Purchase being made must correspond to a Request with the same Item in the same Registry. The action whose design is most affected by this invariant would also be purchase. This action preserves the invariant by confirming the Registry’s existence and ensures that the Item attempting to be purchased does exist within the Registry. By doing so, it prevents any issues with purchases being made for Items that are not in any Request of the Registry. It also allows accurate linking between a Purchase and Request when it comes to the Item’s count. This invariant could be considered more important than the previous one as it keeps the entire system of having a Registry in check. Without this condition, items that were not requested could be purchased in large quantities while ones that were requested could be neglected. There could be a set of Purchases that may not correspond to any of the set of Requests. This would damage the ability to maintain a count of items purchased and would defeat the entire purpose of having a Registry. 

### 2. Can you identify an action that potentially breaks this important invariant, and say how this might happen? How might this problem be fixed?
An action that could potentially break the important invariant would be removeItem. A situation where it could be broken is if an item that was being requested is removed but has already been purchased by others. By doing this, it means there would be a set of Purchases that do not correspond to any Requests, which breaks the invariant and is problematic for the purpose of having a Registry. This problem can be fixed by adding in more to the required aspect of the action, which is that the item cannot be removed after there exists a Purchase for it. In this setup, the user would have to accept the initial amount they provided for how much they wanted of the Item in order to assist in preserving the invariant and basic purpose of their Registry. It may also be worth adding in an action that pauses an item or allows for editing the count rather than removing it in order to maintain that record of the Purchases. 

### 3. The operational principle describes the typical scenario in which the registry is opened and eventually closed. But a concept specification often allows other scenarios. By reading the specs of the concept actions, say whether a registry can be opened and closed repeatedly. What is a reason to allow this?
By reading the species of the concept actions, specifically open and close, the registry can be opened and closed repeatedly. A reason to allow that ability is that a user may find that they have to delay or temporarily cancel the event they are receiving gifts for, which means they would not want to receive purchases in the meantime. They can then easily reopen the registry to avoid having to start over for the same event. 

### 4. There is no action to delete a registry. Would this matter in practice?
This could matter in practice, especially for active users. Users will have a lifetime of parties and though once they mark their registry as inactive it would not show up for others, it would for them. It could add unnecessary clutter for something they may not plan on ever taking inspiration from again. If the User has to pay to use the app based on the number of registries they have, that would also be a problematic case. The User may have also created a registry on accident and having an ‘undo’ with the form of the delete is fundamental for any app. 

### 5. What are two common queries likely to be executed against the concept state? (Hint: one is executed by a registry owner, and one by a giver of a gift.)
One query executed by a registry owner would be checking which items have been purchased and by which person. As the one receiving the gift, the owner would be very interested in finding this out whether it be for purely informational purposes or wanting to show gratitude with directed messages.

Another common query executed by the giver of a gift would be to check which items are still left to be able to purchase. The point of a registry is to give requested items and if some requests have already been fulfilled, a person would want to find one that has not been fulfilled yet.

### 6. A common feature of gift registries is to allow the recipient to choose not to see purchases so that an element of surprise is retained. How would you augment the concept specification to support this?
One could augment the concept specification with the state by adding a surprise purchases visibility flag to the Registry. The updated state for the Registry itself would be:

> a set of Registries with
>> an owner User\
>> an active Flag\
> a set of Requests\
>> a surprise purchases visibility Toggle 
     
Then, there would be corresponding actions for it, the same as the open and close actions that work for the Flag. Additionally, the create action should set the Toggle as false on default. 

> hide (owner: User, registry: Registry)
>> requires registry exists and belongs to owner\
>> effects make registry’s purchases invisible to owner User

> reveal (owner: User, registry: Registry)
>> requires registry exists and it is on\
>> effects make registry's purchases visible to owner User

### 7. The User and Item types are specified as generic parameters. The Item type might be populated by SKU codes, for example. Explain why this is preferable to representing items with their names, descriptions, prices, etc.

It is far preferable to using names, descriptions, prices, or another element since it ensures the right Item is being requested. For instance, the owner User may be requesting diapers for a baby shower but may have a specific brand, material, or color they want them in. Though the User could input the name or description to a structured element, it adds an extra layer of work for both the User and those looking to purchase. Instead, the User can select their Item specifically, which would be populated by SKU codes that are unique and will directly point to the exact Item they are looking for. This removes a burden on both sides. 

## Exercise 2

### 1. Complete the definition of the concept state.
> A set of Users with
>> A username String\
>> A password String

### 2. Write a requires/effects specification for each of the two actions. (Hints: The register action creates and returns a new user. The authenticate action is primarily a guard, and doesn’t mutate the state.)
> register (username: String, password: String): (user: User)
>> requires the username to not already exist\
>> effects create a new username with the corresponding password

> authenticate (username: String, password: String): (user: User)
>> requires username to exist and for password to correspond to it

### 3. What essential invariant must hold on the state? How is it preserved?
The essential invariant would be that there must be at most one user with a given username. This avoids duplicate usernames and the confusion that would come with it. It is preserved by the register action, which checks if the username that a User is attempting to register with already exists. It will not allow it if the username already exists. 

### 4. One widely used extension of this concept requires that registration be confirmed by email. Extend the concept to include this functionality. (Hints: you should add (1) an extra result variable to the register action that returns a secret token that (via a sync) will be emailed to the user; (2) a new confirm action that takes a username and a secret token and completes the registration; (3) whatever additional state is needed to support this behavior.)
> A set of Users with
>> A username String\
>> A password String\
>> An email String\
>> A confirmed flag Boolean\
>> A token String

> register (username: String, password: String, email: String, confirmed: Boolean): (user: User, token: String)
>> requires the username and email to not already exist
>> effects creates a new username with the corresponding password and sets confirmed to false, sends a token

> authenticate (username: String, password: String): (user: User)
>> requires username to exist and for password to correspond to it

> confirm (username: String, token: String)
>> requires the username and email to exist, as well as for the confirmation token the user received via email to be valid
>> effects confirmed is set to true

## Exercise 3

### 1. A concept specification for PersonalAccessToken and a succinct note about how it differs from PasswordAuthentication and how you might change the GitHub documentation to explain this.

A PersonalAccessToken is different in the sense that it is tailored to programmers who are looking for a secure manner to, for instance, push updates to their code. They are not logging into their account using a regular password or looking to change anything specifically in their account, it adds an extra level of protection for items such as repositories. In the Github documentation, it would be most ideal to remove all comparisons between a password and a token, particularly the line “Treat your access tokens like passwords.” This, similar to Twitter’s problematic favorite, creates confusing comparisons for a feature that does share some similarities with a password but has a more specific and unique purpose. Instead, the documentation should emphasize the importance of keeping the token secure and highlight the fun fact that though a username is required to enter when using the token, the token alone is used for authentication. 

> concept PersonalAccessToken
>> purpose limit Github programming access to known users\
>> principle provided the user has an account, Github can generate them a token that replaces the use of a password in a command line and API setting

> state
>> a set of Users with 
>>> a username String\
>>> a password String\
>>> a set of Tokens

>> a set of Tokens with
>>> a value String\
>>> an expiration String\
>>> a scope [String]\
>> a note String

> actions
>> generate (user: User, a scope: [String], expiration: String, note: String): (token: Token)
>>> requires User must be logged in\
>>> effects creates a value and adds the inputted settings for the token\

>> authenticate (username: String, token: Token)\
>>> requires User must have an unexpired and valid token

>> delete (user: User, token: Token): (user: User)\
>>> requires token belongs to the User\
>>> effects removes token from User’s set of Tokens

## Exercise 4

### 1. Three concept specifications, with any subtleties explained in brief additional notes.

#### URL Shortener

> purpose create a short and easy to type alias for a longer URL\
> principle a mapping is set up for an alias and the original URL. When a user searches for the shortened URL, they are directed to their original URL.

> state
>> A Mapping with
>>> an alias String\
>>> an originalURL String

> actions 
>> shorten (originalURL: String, alias: String): (shortenedURL: String)
>>> require  if entering alias, for it to not already exist\
>>> effect creates a shortened URL using the alias the user entered

>> shorten (originalURL: String): (shortenedURL: String, Mapping: Mapping)
>>> effect creates a shortened URL by setting the alias as an automatically generated one, sets up the Mapping with that generated alias

The act of searching takes place outside of the application, so it was not included as it would be a manner of testing the functionality rather than being a fundamental part of the concept. There are also two shorten actions depending on if the user enters their custom alias or opts for it to be generated. I also did not included shortenedURL in the state since it is just a fixed string tinyurl.com part + the alias. 

#### Billable Hours Tracking

> purpose keep track of hours spent working on a project for a client\
> principle an employee can begin their session by selecting and inputting a description of what they’ll be working on. They can then end their session by marking it complete. The system keeps a record of the starting and ending times. 

> state
> a set of Sessions with 
>> an employee User\
>> a project String\
>> a description String\
>> a startTime String\
>> an endTime String\
>> a status String

> actions
>> start (employee: User, project: String, description: String, status: String): (session: Session)
>>> require there is not currently another active session and that the selected project is a valid one\
>>> effects creates an active status session with the current timestamp startTime

>> cancel (employee: User): (session: Session)
>>> require there is an active session\
>>> effects removes session from view without keeping track of its duration, marks status as cancelled

>> end (employee: User): (session: Session)
>>> require there is an active session\
>>> effects ends session with the current timestamp endTime, marks it as complete

>> autoEnd (user: User): (session : Session)
>>> require a session has gone more than 8 hours as active\
>>> effects ends session with the current timestamp endTime, marks it as complete

I selected 8 hours as that is the duration of a typical workday since in majority of cases if a user forgets to end a work session, it is due to them having spent the day with them and forgetting to clock out before leaving work.  


#### Conference Room Booking
