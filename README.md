
# Refactoring Techniques

## Long Method

A method contains too many lines of code. Generally, any method longer than ten lines should make you start asking questions. As a rule of thumb, if you feel the need to comment on something inside a method, you should take this code and put it in a new method.

### Extract Method

##### Problem

You have a code fragment that can be grouped together.

```python
def printOwing(self):
    self.printBanner()

    # print details
    print("name:", self.name)
    print("amount:", self.getOutstanding())
```

##### Solution

Move this code to a separate new method (or function) and replace the old code with a call to the method.

```python
def printOwing(self):
    self.printBanner()
    self.printDetails(self.getOutstanding())

def printDetails(self, outstanding):
    print("name:", self.name)
    print("amount:", outstanding)
```

> If you see that a local variable changes in your extracted code in some way, this may mean that this changed value will be needed later in your main method. Double-check! And if this is indeed the case, return the value of this variable to the main method to keep everything functioning.

### Replace Temp with Query

If the variables are declared prior to the code that you’re extracting, you will need to pass these variables to the parameters of your new method in order to use the values previously contained in them. Sometimes it’s easier to get rid of these variables by resorting to Replace Temp with Query.

##### Problem

You place the result of an expression in a local variable for later use in your code.

```python
def calculateTotal():
    basePrice = quantity * itemPrice
    if basePrice > 1000:
        return basePrice * 0.95
    else:
        return basePrice * 0.98
```

##### Solution

Move the entire expression to a separate method and return the result from it. Query the method instead of using a variable. Incorporate the new method in other methods, if necessary.

```python
def calculateTotal():
    if basePrice() > 1000:
        return basePrice() * 0.95
    else:
        return basePrice() * 0.98

def basePrice():
    return quantity * itemPrice
```

#### Split Temporary Variable

Make sure that a value is assigned to the variable once and only once within the method. If not, use **Split Temporary Variable** to ensure that the variable will be used only to store the result of your expression.

##### Problem

You have a local variable that’s used to store various intermediate values inside a method (except for cycle variables).

```python
temp = 2 * (height + width)
print(temp)
temp = height * width
print(temp)
```

##### Solution

Use different variables for different values. Each variable should be responsible for only one particular thing.

```python
perimeter = 2 * (height + width)
print(perimeter)
area = height * width
print(area)
```

#### Separate Query from Modifier

Make sure that this method only returns a value and doesn’t change the state of the object. If the method affects the visible state of the object, use **Separate Query from Modifier**.

##### Problem

Do you have a method that returns a value but also changes something inside an object?

```python
def getTotalOutstandingAndSetReadyForSummaries():
    pass
```

##### Solution

Split the method into two separate methods. As you would expect, one of them should return the value and the other one modifies the object.

```python
def getTotalOutstanding():
    pass

def setReadyForSummaries():
    pass
```

### Preserve Whole Object

##### Problem

You get several values from an object and then pass them as parameters to a method.

```python
low = daysTempRange.getLow()
high = daysTempRange.getHigh()
withinPlan = plan.withinRange(low, high
```
##### Solution

Instead, try passing the whole object.

```python
withinPlan = plan.withinRange(daysTempRange)
```

### Replace Method with Method Object

##### Problem

You have a long method in which the local variables are so intertwined that you can’t apply Extract Method.

```python
class Order:
    # ...
    def price(self):
        primaryBasePrice = 0
        secondaryBasePrice = 0
        tertiaryBasePrice = 0
        # Perform long computation.
```

##### Solution

Transform the method into a separate class so that the local variables become fields of the class. Then you can split the method into several methods within the same class.

```python
class Order:
    # ...
    def price(self):
        return PriceCalculator(self).compute()


class PriceCalculator:
    def __init__(self, order):
        self._primaryBasePrice = 0
        self._secondaryBasePrice = 0
        self._tertiaryBasePrice = 0
        # Copy relevant information from the
        # order object.

    def compute(self):
        # Perform long computation.
```

### Decompose Conditional

##### Problem 

You have a complex conditional (if-then/else or switch).

```python
if date.before(SUMMER_START) or date.after(SUMMER_END):
    charge = quantity * winterRate + winterServiceCharge
else:
    charge = quantity * summerRate
```

##### Solution

Decompose the complicated parts of the conditional into separate methods: the condition, then and else.

```python
if isSummer(date):
    charge = summerCharge(quantity)
else:
    charge = winterCharge(quantity)
```