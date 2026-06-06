# 📉 Material Shortage (Including Safety Stock)

**Description:**
A vital procurement measure that calculates the absolute shortage of raw materials by comparing the current Stock on Hand against the total target (which includes both production demand and the required Safety Stock buffer).

```dax
Material Requirements (ABS)+SS = 
// 1. Define operational targets
VAR MatDemand = [Material Demand for Selected Item]
VAR SafetyStock = [Total Safety Stock] 
VAR TotalTarget = MatDemand + SafetyStock

// 2. Assess current inventory
VAR MatStock = SUM('Material_Inv_F'[Quantity])

// 3. Calculate Variance
VAR Shortage = TotalTarget - MatStock

// Return the absolute value of the shortage
RETURN
    ABS(Shortage)
