# S04-Code-Smells
## Code Smell 1 - Speculative Generality - Ethan Kwak
### Intro 

Sometimes people overprepare by adding features that don’t need to exist. By adding all these special cases and hooks that never get used it becomes harder to organize and maintain. 

### Prevention and Refactoring 

**Collapse Hierarchy** is when you are refactoring a hierarchy and as the hierarchy evolves a class and a parent are no longer different enough to be kept separate resulting in a merger. **Inline Function** is when you have a function that’s code is as clear as its name so it does not need to exist or when simple delegation is given to another function that does not need to exist. **Inline Class** is when a class is no longer needed and shouldn’t be taking up space as all of its responsibilities have been allocated elsewhere so usually the runt class gets absorbed by another one. **Change Function Declaration** is when you alter a function’s name or parameters to improve clarity and visibility without changing the function’s observable behaviors. **Remove Dead Code** is when you remove code that no longer serves a purpose whether it be commented out or does nothing.

### Code Examples Across Languages

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

This is a snippet of Python code that exhibits **Collapse Hierarchy**. As we can see the Salesman class is redundant of the Employee class so we would remove it altogether moving the get_commision function into the Employee class.

function processPayment(amount) {
  if (amount <= 0) {
    return "Invalid amount";
    console.log("This will never print"); 
  }
  return `Charged $${amount}`;
}

This is a snippet of Javascript code that has **Dead Code**. Since console.log will never print due to it being right after a return statement it no longer serves a purpose and should be removed.

## Code Smell 2 - Long Function - Dan Pokhrel
### Intro 

The longer a function gets the harder it becomes to understand. This typically arises from adding functionality to one function until it becomes too big. Long functions demand the reader to process a bigger context and make it harder to understand what it does. 

### Prevention and Refactoring 

Most of the time, long functions can be refactored through Extract Function. Long functions are typically composed of multiple related tasks which can be extracted into their own functions. This smell can also be avoided by keeping related behaviors into their own small functions to avoid them building up into one long function. 

### Code Examples Across Languages



## Code Smell 3 
### Intro 


### Prevention and Refactoring 


### Code Examples Across Languages



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

### Code Examples Across Languages 
Python:
# checkout.py
def checkout(price):
    return price * 1.08

# billing.py
def billing(price):
    return price * 1.08

# receipt.py
def receipt(price):
    return price * 1.08

----------------------------------------------------------------------------------

JavaScript: 
function checkout(price) {
    return price * 1.08;
}

function billing(price) {
    return price * 1.08;
}

function receipt(price) {
    return price * 1.08;
}
