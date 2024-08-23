
# Autowiring Constructors

If a class ***only*** has a default constructor:
- Nothing to annotate

If a class has only one non-default constructor:
- It is the only constructor available, Spring will call it
- `@Autowired` is optional

If a class has *more than one* constructor:
- Spring invokes zero-argument constructors by default (if it exists)
- Or you must annotate with `@Autowired` the one you want Spring to use

# About Component Scanning

- Components are scanned at startup
	- JAR dependencies also scanned
	- Could result in slower startup time if too many files scanned

# Component Scanning - Best Practises

- Really Bad:
	- `@ComponentScan({"org", "com"})`
- Still bad:
	- `@ComponentScan("com")`
- OK:
	- `@ComponentScan("com.bank.app")`
- Optimised:
	- `@ComponentScan({"com.bank.app.repository", "com.bank.app.service", "com.bank.app.controller"})`

# Mixing Java Config and Annotations

Common approach selecting when to use:
- Annotations:
	- In some cases, Spring will give no other choice!
	- Stereotype annotations
- Java Configuration:
	- When it is desired or required to keep beans decoupled from Spring (Legacy code)
	- When managing configurations in a single logical location is an important issue
	- Can be used from all classes