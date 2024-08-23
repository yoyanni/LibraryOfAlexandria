# How do you collect metrics?

![[Pasted image 20240816161745.png]]

# Custom Metrics - What are they?

![[Pasted image 20240816161829.png]]
# Hierarchical vs Dimensional Metrics

![[Pasted image 20240816162032.png]]
# Hierarchical Metrics

![[Pasted image 20240816162152.png]]

# Dimensional Metrics

![[Pasted image 20240816162328.png]]

# MeterRegistry - Timer

- timer object used explicitly - `placeOrder()` method is intrusive
- `@Timed`annotation - not intrusive

![[Pasted image 20240816162451.png]]

# Recording to a `DistributionSummary`

- instead of using the MeterRegistry to create a DistributionSummary, the **builder** is used instead and then the registered with the MeterRegistry.

![[Pasted image 20240816162801.png]]

# Example of DistributionSummary

![[Pasted image 20240816162923.png]]