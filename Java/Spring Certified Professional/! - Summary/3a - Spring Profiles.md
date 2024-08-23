# What are Profiles?

Profiles represent environment and are used when you want to group specific Beans together for that specific environment.

They can also be used for specific implementations:

- jdbc
- jpa

OR deployment platforms:

- on-premise
- cloud

[[1 - Spring Profiles#Beans can be grouped into Profiles|More Details and Picture]]

# How to use Profiles?

You can use the `@Profile` annotation to specify the profile it belongs to. You have to activate profiles at run-time and you can do that in two ways:

- Using a System property via the command-line
	- `-Dspring.profiles.active=embedded,jpa`
- Using a System property programmatically

[[1 - Spring Profiles#Ways to Activate Profiles|Details]]
## Where can you use them?

You can set them either on the:

- Class Level
- Method Level

You can also use the `!` when specifying a Profile to state that it should not be used with the specific profile mentioned.

[[1 - Spring Profiles#Defining Profiles|Pictures]]


# What can `@Profile` do?

[[1 - Spring Profiles#Property Source Selection|Answer]]
