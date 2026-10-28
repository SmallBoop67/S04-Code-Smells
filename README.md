# S04-Code-Smells
## Code Smell 1 - Speculative Generality - Ethan Kwak
### Intro 

Sometimes people overprepare by adding features that don’t need to exist. By adding all these special cases and hooks that never get used it becomes harder to organize and maintain. 

### Prevention and Refactoring 

**Collapse Hierarchy** is when you are refactoring a hierarchy and as the hierarchy evolves a class and a parent are no longer different enough to be kept separate resulting in a merger. **Inline Function** is when you have a function that’s code is as clear as its name so it does not need to exist or when simple delegation is given to another function that does not need to exist. **Inline Class** is when a class is no longer needed and shouldn’t be taking up space as all of its responsibilities have been allocated elsewhere so usually the runt class gets absorbed by another one. **Change Function Declaration** is when you alter a function’s name or parameters to improve clarity and visibility without changing the function’s observable behaviors. **Remove Dead Code** is when you remove code that no longer serves a purpose whether it be commented out or does nothing.

### Code Examples Across Languages

```python
class Employee:

  def __init__(self, name, phone_number):
    self.name = name
    self.phone_number = phone_number

  def get_contact_info(self):
    return self.phone_number


class Salesman(Employee):

  def __init__(self, name, phone_number, commission_rate):
    super().__init__(name, phone_number)
    self.commission_rate = commission_rate

  def get_commission(self):
    return self.commission_rate
```
This is a snippet of Python code that exhibits **Collapse Hierarchy**. As we can see the Salesman class is redundant of the Employee class so we would remove it altogether moving the get_commision function into the Employee class.

```python
class Employee:
    def __init__(self, name, phone_number, commission_rate):
        self.name = name
        self.phone_number = phone_number
        self.commission_rate = commission_rate

    def get_contact_info(self):
        return self.phone_number

    def get_commission(self):
        return self.commission_rate
```
```javascript
function processPayment(amount) {
  if (amount <= 0) {
    return "Invalid amount";
    console.log("This will never print"); 
  }
  return `Charged $${amount}`;
}
```

This is a snippet of Javascript code that has **Dead Code**. Since console.log will never print due to it being right after a return statement it no longer serves a purpose and should be removed.

```javascript
function processPayment(amount) {
  if (amount <= 0) {
    return "Invalid amount";
  }
  return `Charged $${amount}`;
}
```
## Code Smell 2 - Long Function - Dan Pokhrel
### Intro 

The longer a function gets the harder it becomes to understand. This typically arises from adding functionality to one function until it becomes too big. Long functions demand the reader to process a bigger context and make it harder to understand what it does. 

### Prevention and Refactoring 

Most of the time, long functions can be refactored through Extract Function. Long functions are typically composed of multiple related tasks which can be extracted into their own functions. This smell can also be avoided by keeping related behaviors into their own small functions to avoid them building up into one long function. 

### Code Examples Across Languages



## Code Smell 3 - Loops - Daniel Tubman
### Intro 
Loops have been a well used staple of programming for a long time as they are simple to understand and they used to be the only option. The code smell of loops starts to arise when they are being used to do multiple things at once which make it hard for readers to understand and trace the function. Because of this, nowadays there are better options for implementing large and complex functions rather than using a loop as the problem isn’t the use of loops in general but rather loops being used for too many things at once.

### Prevention and Refactoring 
To avoid this code smell, you should be expressing collection processing as a pipeline of operations like filter and map instead of writing a loop first since each step then names its own job. It is also important to pay attention to loops that fill an accumulator or do several jobs at once.
To fix the code smell when you already have a loop, Fowler recommends using the “Replace Loop with Pipeline” practice. First, you create a new variable for the loop’s collection. Next, you replace each piece of the loop with a pipeline operation, all the while testing after each change. Finally, once everything has been converted to a pipeline operation, you then remove the loop and, if the loop was filling an accumulator, assign the new pipeline’s result to the accumulator’s variable.

### Code Examples Across Languages

### JavaScript

Smell (Page 232, in Chapter 8 of Fowler's Refactoring):
```javascript 
function acquireData(input) {
const lines = input.split("\n");
let firstLine = true;
const result = [];
for (const line of lines) {
if (firstLine) {
firstLine = false;
continue;
}
if (line.trim() === "") continue;
const record = line.split(",");
if (record[1].trim() === "India") {
result.push({city: record[0].trim(), phone: record[2].trim()});
}
}
return result;
}
``` 
In this JavaScript example, the code smell shows up in the form of the for-of loop trying to do 4 things at once, skipping the header, dropping blank lines, filtering by country, and building the output objects. Due to this, a lot of variables have to constantly be updated and kept track of which gets messy.

After Replace Loop with Pipeline (Page 236, in Chapter 8 of Fowler's Refactoring): 
```javascript 
function acquireData(input) {
const lines = input.split("\n");
return lines
.slice (1)
.filter (line => line.trim() !== "")
.map (line => line.split(","))
.filter (fields => fields[1].trim() === "India")
.map (fields => ({city: fields[0].trim(), phone: fields[2].trim()}))
;
}
``` 
After applying Fowler’s recommended refactoring method, each job now has its own named step. Another result of this is that the code is much more readable and streamlined with the fluff (firstLine, continue, result) replaced.

----------------------------------------------------------------------------------
### Python
Smell (https://docs.sourcery.ai/reference/refactorings/python/reintroduce-else):
```python 
summer_hats = []
for hat in hats:
    if hat in WINTER_HATS:
        continue
    summer_hats.append(hat)
``` 
In this Python example the code smell shows up in the form of a for-in function doing two things at once, skipping winter hats with continue and building a result by appending to a list. The multiple jobs being performed by the loop makes it harder to parse and it can make it more confusing then it needs to be for a reader when trying to figure out what it does.

After Refactoring: 
```python 
summer_hats = [hat for hat in hats if hat not in WINTER_HATS] 
``` 
After refactoring this example code in a similar way to Fowler’s idea, we now have much more compact and easier to read code. The filter and the construction of the list have been combined together into the declaration of what summer_hats is and the continue and the accumulator have been removed.





## Code Smell 4 - Shotgun Surgery - Waheed Khan
### Intro 
Shotgun Surgery is a code smell that happens when making one small changed requires making one small change requires modifying multiple files, classes,  or functions throughout a program. This usually happens when related code is spread across different parts of a project instead of being organized in one place. For example, imagine an online shopping application where sales tax is calculated separately in the checkout, billing, and receipt systems. If the tax rate changes, the developer would have to update all three systems individually. This becomes a problem because it makes the code harder to maintain, increases the chances of forgetting an update and can introduce bugs when different parts of the program are not updated consistently.

### Prevention and Refactoring 
The best way to prevent Shotgun Surgery is to keep related code together and give each class or function a clear responsibility. 

Some ways to prevent and fix this code smell include: 

-- Move Function: Move functions into the class or module where they logically belong.

-- Move Field: Move related data into the the class that is responsible for using it. 

-- Combine Function into Class: Group related functions into one class so changes can be made in a single location. 

-- Avoid Duplicating Logic: Instead of writing the same calculation in multiple places, create a reusable function. 

Instead of calculating sales tax separately in multiple files, we can create on function responsible for calculating tax. Other parts of the program can call that function whenever they need it. This makes the code easier to maintain because future changes only need to happen in one places.

### Python

Smell:

```python 
# checkout.py
def checkout(price):
    return price * 1.08

# billing.py
def billing(price):
    return round(price * 1.08, 2)

# receipt.py
def receipt(price):
    return f"Total: ${price * 1.08:.2f} (includes 8% tax)"
``` 
After Move Function (the tax logic gathered into one module):

```python
# tax.py
TAX_RATE = 0.08

def with_tax(price):
    return round(price * (1 + TAX_RATE), 2)

def tax_label():
    return f"includes {TAX_RATE:.0%} tax"

# checkout.py
from tax import with_tax
def checkout(price):
    return with_tax(price)

# billing.py
from tax import with_tax
def billing(price):
    return with_tax(price)

# receipt.py
from tax import with_tax, tax_label
def receipt(price):
    return f"Total: ${with_tax(price):.2f} ({tax_label()})"
``` 
----------------------------------------------------------------------------------
### JavaScript

```javascript
// checkout.js
function checkout(price) {
    return price * 1.08;
}

// billing.js
function billing(price) {
    return Math.round(price * 1.08 * 100) / 100;
}

// receipt.js
function receipt(price) {
    return `Total: $${(price * 1.08).toFixed(2)} (includes 8% tax)`;
}
```

After Move Function (the tax logic gathered into one module):

```javascript
// tax.js
export const TAX_RATE = 0.08;

export function withTax(price) {
    return Math.round(price * (1 + TAX_RATE) * 100) / 100;
}

export function taxLabel() {
    return `includes ${TAX_RATE * 100}% tax`;
}

// checkout.js
import { withTax } from "./tax.js";

export function checkout(price) {
    return withTax(price);
}

// billing.js
import { withTax } from "./tax.js";

export function billing(price) {
    return withTax(price);
}

// receipt.js
import { withTax, taxLabel } from "./tax.js";

export function receipt(price) {
    return `Total: $${withTax(price).toFixed(2)} (${taxLabel()})`;
}
```
