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
The longer a function gets the harder it becomes to understand. Flower explains, *"the programs that live the best and longest are those with short functions."* Long functions demand the reader to process a bigger context which makes it harder to understand what the code does. This smell typically arises from adding functionality to one function until it becomes too big. There's also the psychological aspect of codebases with short functions making it feels like it all delegation with little to no computation. 

### Prevention and Refactoring 
Most of the time, long functions can be refactored through Extract Function. Long functions are typically composed of multiple steps or tasks which can be extracted into their own functions. This smell can also be avoided by keeping related behaviors into their own small functions to avoid them building up into one long function. Flower states, 
> “Whenever we feel the need to comment something, we write a function instead.”

### Code Examples Across Languages

```rust
    pub fn ray_cast(
        &mut self,
        mode: i32,
        ox: f32,
        oy: f32,
        oz: f32,
        dx: f32,
        dy: f32,
        dz: f32,
    ) -> Vec<i32> {
        if self.arr.is_none() {
            return vec![
                0,
                i32::MAX,
                i32::MAX,
                i32::MAX,
                0,
                i32::MAX,
                i32::MAX,
                i32::MAX,
            ];
        }
        let arr = self.arr.as_mut().unwrap();

        // Transform to local coordinates
        let mut local_ox = ox - self.origin.0 as f32;
        let mut local_oy = oy - self.origin.1 as f32;
        let mut local_oz = oz - self.origin.2 as f32;

        // Compute entry point into the chunk bounding box
        let mut t_min = 0.0f32;
        let mut t_max = f32::INFINITY;
        let mut t_min_x = f32::NEG_INFINITY;
        let mut t_min_y = f32::NEG_INFINITY;
        let mut t_min_z = f32::NEG_INFINITY;

        // For x
        if dx != 0.0 {
            let t1 = (0.0 - local_ox) / dx;
            let t2 = (SIZE as f32 - local_ox) / dx;
            t_min_x = t1.min(t2);
            let t_max_x = t1.max(t2);
            t_min = t_min.max(t_min_x);
            t_max = t_max.min(t_max_x);
        } else if local_ox < 0.0 || local_ox >= SIZE as f32 {
            return vec![
                0,
                i32::MAX,
                i32::MAX,
                i32::MAX,
                0,
                i32::MAX,
                i32::MAX,
                i32::MAX,
            ];
        }

        // For y
        if dy != 0.0 {
            let t1 = (0.0 - local_oy) / dy;
            let t2 = (SIZE as f32 - local_oy) / dy;
            t_min_y = t1.min(t2);
            let t_max_y = t1.max(t2);
            t_min = t_min.max(t_min_y);
            t_max = t_max.min(t_max_y);
        } else if local_oy < 0.0 || local_oy >= SIZE as f32 {
            return vec![
                0,
                i32::MAX,
                i32::MAX,
                i32::MAX,
                0,
                i32::MAX,
                i32::MAX,
                i32::MAX,
            ];
        }

        // For z
        if dz != 0.0 {
            let t1 = (0.0 - local_oz) / dz;
            let t2 = (SIZE as f32 - local_oz) / dz;
            t_min_z = t1.min(t2);
            let t_max_z = t1.max(t2);
            t_min = t_min.max(t_min_z);
            t_max = t_max.min(t_max_z);
        } else if local_oz < 0.0 || local_oz >= SIZE as f32 {
            return vec![
                0,
                i32::MAX,
                i32::MAX,
                i32::MAX,
                0,
                i32::MAX,
                i32::MAX,
                i32::MAX,
            ];
        }

        if t_min > t_max {
            return vec![
                0,
                i32::MAX,
                i32::MAX,
                i32::MAX,
                0,
                i32::MAX,
                i32::MAX,
                i32::MAX,
            ];
        }

        let entry_step_x = if t_min > 0.0 && dx != 0.0 && (t_min - t_min_x).abs() < 1e-6 {
            if dx > 0.0 {
                1
            } else {
                -1
            }
        } else {
            0
        };
        let entry_step_y = if t_min > 0.0 && dy != 0.0 && (t_min - t_min_y).abs() < 1e-6 {
            if dy > 0.0 {
                1
            } else {
                -1
            }
        } else {
            0
        };
        let entry_step_z = if t_min > 0.0 && dz != 0.0 && (t_min - t_min_z).abs() < 1e-6 {
            if dz > 0.0 {
                1
            } else {
                -1
            }
        } else {
            0
        };

        // Move to entry point
        local_ox += dx * t_min;
        local_oy += dy * t_min;
        local_oz += dz * t_min;

        // Current voxel
        let mut x = local_ox.floor() as i32;
        let mut y = local_oy.floor() as i32;
        let mut z = local_oz.floor() as i32;

        // Step directions
        let step_x = if dx > 0.0 {
            1
        } else if dx < 0.0 {
            -1
        } else {
            0
        };
        let step_y = if dy > 0.0 {
            1
        } else if dy < 0.0 {
            -1
        } else {
            0
        };
        let step_z = if dz > 0.0 {
            1
        } else if dz < 0.0 {
            -1
        } else {
            0
        };

        let mut face_step_x = entry_step_x;
        let mut face_step_y = entry_step_y;
        let mut face_step_z = entry_step_z;
        if face_step_x == 0 && face_step_y == 0 && face_step_z == 0 {
            if dx.abs() >= dy.abs() && dx.abs() >= dz.abs() {
                face_step_x = step_x;
            } else if dy.abs() >= dx.abs() && dy.abs() >= dz.abs() {
                face_step_y = step_y;
            } else {
                face_step_z = step_z;
            }
        }

        // tMax calculations
        let mut t_max_x = if step_x > 0 {
            ((x as f32) + 1.0 - local_ox) / dx
        } else if step_x < 0 {
            ((x as f32) - local_ox) / dx
        } else {
            f32::INFINITY
        };
        let mut t_max_y = if step_y > 0 {
            ((y as f32) + 1.0 - local_oy) / dy
        } else if step_y < 0 {
            ((y as f32) - local_oy) / dy
        } else {
            f32::INFINITY
        };
        let mut t_max_z = if step_z > 0 {
            ((z as f32) + 1.0 - local_oz) / dz
        } else if step_z < 0 {
            ((z as f32) - local_oz) / dz
        } else {
            f32::INFINITY
        };

        // tDelta
        let t_delta_x = if step_x != 0 {
            step_x as f32 / dx
        } else {
            f32::INFINITY
        };
        let t_delta_y = if step_y != 0 {
            step_y as f32 / dy
        } else {
            f32::INFINITY
        };
        let t_delta_z = if step_z != 0 {
            step_z as f32 / dz
        } else {
            f32::INFINITY
        };

        // Traverse
        for _ in 0..(SIZE * 3) {
            if x >= 0 && x < SIZE as i32 && y >= 0 && y < SIZE as i32 && z >= 0 && z < SIZE as i32 {
                let idx = xyz_idx(x as u8, y as u8, z as u8);
                let voxel_id = arr[idx];
                if voxel_id != 0 {
                    let mut modified_x = x;
                    let mut modified_y = y;
                    let mut modified_z = z;

                    if mode == 0 {
                        arr[idx] = 0;
                    } else if mode > 0 {
                        let target_x = x - face_step_x;
                        let target_y = y - face_step_y;
                        let target_z = z - face_step_z;
                        if target_x >= 0
                            && target_x < SIZE as i32
                            && target_y >= 0
                            && target_y < SIZE as i32
                            && target_z >= 0
                            && target_z < SIZE as i32
                        {
                            let target_idx =
                                xyz_idx(target_x as u8, target_y as u8, target_z as u8);
                            arr[target_idx] = mode as u8;
                            modified_x = target_x;
                            modified_y = target_y;
                            modified_z = target_z;
                        }
                    }

                    let status = if modified_x == 0
                        || modified_x == (SIZE as i32 - 1)
                        || modified_y == 0
                        || modified_y == (SIZE as i32 - 1)
                        || modified_z == 0
                        || modified_z == (SIZE as i32 - 1)
                    {
                        2
                    } else if modified_x == 1
                        || modified_x == (SIZE as i32 - 2)
                        || modified_y == 1
                        || modified_y == (SIZE as i32 - 2)
                        || modified_z == 1
                        || modified_z == (SIZE as i32 - 2)
                    {
                        1
                    } else {
                        0
                    };

                    return vec![
                        voxel_id as i32,
                        x,
                        y,
                        z,
                        status,
                        modified_x,
                        modified_y,
                        modified_z,
                    ];
                }
            } else {
                return vec![
                    0,
                    i32::MAX,
                    i32::MAX,
                    i32::MAX,
                    0,
                    i32::MAX,
                    i32::MAX,
                    i32::MAX,
                ];
            }

            if t_max_x < t_max_y && t_max_x < t_max_z {
                x += step_x;
                face_step_x = step_x;
                face_step_y = 0;
                face_step_z = 0;
                t_max_x += t_delta_x;
            } else if t_max_y < t_max_z {
                y += step_y;
                face_step_x = 0;
                face_step_y = step_y;
                face_step_z = 0;
                t_max_y += t_delta_y;
            } else {
                z += step_z;
                face_step_x = 0;
                face_step_y = 0;
                face_step_z = step_z;
                t_max_z += t_delta_z;
            }
        }

        vec![
            0,
            i32::MAX,
            i32::MAX,
            i32::MAX,
            0,
            i32::MAX,
            i32::MAX,
            i32::MAX,
        ]
    }
}
```

```c#
void Simulate()
{
    Rect2 renderRect = data_node.get_render_rect();
    renderRect.Position += new Vector2(1, 1);
    renderRect.Size -= new Vector2(2, 2);

    Dictionary<Vector2, float> changes = new Dictionary<Vector2, float>();

    for (int x = (int)renderRect.Position.x; x < renderRect.End.x; x++)
    for (int y = (int)renderRect.Position.y; y < renderRect.End.y; y++)
    {
        Block block = data_node.get_blockb(x, y, layer.Id);
        if (block.Id != BlockId) continue;

        if (block.liquid == 0) block.liquid = 1;
        if (block.liquid < MinValue)
        {
            block.liquid = 0f;
            continue;
        };
        if (block.settled) continue;

        float startValue = block.liquid;
        float remainingValue = block.liquid;
        float flow = 0;

        ref Block top = ref data_node.get_blockb(x, y-1, layer.Id);
        ref Block right = ref data_node.get_blockb(x+1, y, layer.Id);
        ref Block bottom = ref data_node.get_blockb(x, y+1, layer.Id);
        ref Block left = ref data_node.get_blockb(x-1, y, layer.Id);

        Vector2 curPos = new Vector2(x, y);
        Vector2 topPos = new Vector2(x, y-1);
        Vector2 rightPos = new Vector2(x+1, y);
        Vector2 bottomPos = new Vector2(x, y+1);
        Vector2 leftPos = new Vector2(x-1, y);

        // Initilize Changes
        if (!changes.ContainsKey(curPos)) changes[curPos] = 0;
        if (!changes.ContainsKey(topPos)) changes[topPos] = 0;
        if (!changes.ContainsKey(rightPos)) changes[rightPos] = 0;
        if (!changes.ContainsKey(bottomPos)) changes[bottomPos] = 0;
        if (!changes.ContainsKey(leftPos)) changes[leftPos] = 0;


        if (VerticalOnly)
        {
            if (bottom.Id == 0)
            {
                changes[new Vector2(x, y)] = -1;
                changes[new Vector2(x, y+1)] = 1;
            }
            
            continue;
        }


        if (bottom.Id == 0 || bottom.Id == BlockId)
        {
            flow = CalcVertFlow(block.liquid, bottom) - bottom.liquid;
            if (bottom.liquid > 0 && flow > MinFlow)
                flow *= FlowSpeed;
            
            flow = Mathf.Max (flow, 0);
            if (flow > Mathf.Min(MaxFlow, block.liquid)) 
                flow = Mathf.Min(MaxFlow, block.liquid);
            
            if (flow != 0)
            {
                remainingValue -= flow;
                changes[curPos] -= flow;
                changes[bottomPos] += flow;
                bottom.settled = false;
    }
        }

        if (remainingValue < MinValue)
        {
            changes[curPos] -= remainingValue;
            continue;
        }


        //Flow left
        if (left.Id == 0 || left.Id == BlockId)
        {
            flow = (remainingValue - left.liquid) / 4f;
            if (flow > MinFlow)
                flow *= FlowSpeed;

            flow = Mathf.Max (flow, 0);
            if (flow > Mathf.Min(MaxFlow, remainingValue)) 
                flow = Mathf.Min(MaxFlow, remainingValue);

            if (flow != 0) {
                remainingValue -= flow;
                changes[curPos] -= flow;
                changes[leftPos] += flow;
                left.settled = false;
            }
        }

        if (remainingValue < MinValue)
        {
            changes[curPos] -= remainingValue;
            continue;
        }


        // Flow right
        if (right.Id == 0 || right.Id == BlockId)
        {
            flow = (remainingValue - right.liquid) / 3f;										
            if (flow > MinFlow)
                flow *= FlowSpeed; 

            // constrain flow
            flow = Mathf.Max (flow, 0);
            if (flow > Mathf.Min(MaxFlow, remainingValue)) 
                flow = Mathf.Min(MaxFlow, remainingValue);
            
            // Adjust temp values
            if (flow != 0) {
                remainingValue -= flow;
                changes[curPos] -= flow;
                changes[rightPos] += flow;
                right.settled = false;
            } 
        }

        if (remainingValue < MinValue)
        {
            changes[curPos] -= remainingValue;
            continue;
        }


        //Flow up
        if (top.Id == 0 || top.Id == BlockId)
        {
            flow = remainingValue - CalcVertFlow(remainingValue, top); 
            if (flow > MinFlow)
                flow *= FlowSpeed; 

            flow = Mathf.Max (flow, 0);
            if (flow > Mathf.Min(MaxFlow, remainingValue)) 
                flow = Mathf.Min(MaxFlow, remainingValue);

            if (flow != 0) {
                remainingValue -= flow;
                changes[curPos] -= flow;
                changes[topPos] += flow;
                top.settled = false;
            } 
        }

        if (remainingValue < MinValue)
        {
            changes[curPos] -= remainingValue;
            continue;
        }

        if (startValue == remainingValue)
        {
            block.settle_count++;
            if (block.settle_count >= 10)
                block.settled = true;
        }else{
            top.settled = false;
            right.settled = false;
            bottom.settled = false;
            left.settled = false;
        }
        
        if (!block.settled)
            block.settle_count = 0;
    }

    foreach (Vector2 change in changes.Keys)
    {
        float value = changes[change];
        ref Block block = ref data_node.get_blockb((int)change.x, (int)change.y, layer.Id);
        block.liquid += value;

        if (block.liquid < MinValue && block.Id==BlockId)
            block.Id = 0;
        
        if (block.Id == 0 && block.liquid >= MinValue)
            block.Id = BlockId;
    }

    Update();
}
```


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
