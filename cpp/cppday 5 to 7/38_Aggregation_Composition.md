# 🏗️ Aggregation and Composition in C++

## Table of Contents
1. [Has-A Relationship](#has-a-relationship)
2. [Composition (Strong Ownership)](#composition-strong-ownership)
3. [Aggregation (Weak Association)](#aggregation-weak-association)
4. [Constructor/Destructor Order with Composition](#constructordestructor-order-with-composition)
5. [Comparison: Aggregation vs Composition](#comparison-aggregation-vs-composition)
6. [Key Takeaways](#key-takeaways)

---

## Has-A Relationship

> **Has-A Relationship**: When one class contains another class as a member, creating a "has-a" relationship.

```
┌─────────────────────────────────────────────────────────────────┐
│           HAS-A vs IS-A RELATIONSHIP COMPARISON                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   IS-A (Inheritance):                                            │
│   ┌─────────────────┐                                            │
│   │   FourWheeler   │  ← Car IS-A FourWheeler                   │
│   └────────┬────────┘                                            │
│            │ inherits                                            │
│            ▼                                                     │
│   ┌─────────────────┐                                            │
│   │      Car        │                                            │
│   └─────────────────┘                                            │
│                                                                  │
│   HAS-A (Composition/Aggregation):                               │
│   ┌─────────────────┐                                            │
│   │      Car        │  ← Car HAS-A Engine                       │
│   │  ┌───────────┐  │                                            │
│   │  │  Engine   │  │ ← Engine is a MEMBER of Car               │
│   │  └───────────┘  │                                            │
│   └─────────────────┘                                            │
│                                                                  │
│   KEY DIFFERENCE:                                                │
│   • IS-A: Sub class is a type of parent class                   │
│   • HAS-A: Class contains another class as member               │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Two Types of Has-A Relationship

| Type | Ownership | Lifetime | Example |
|------|-----------|----------|---------|
| **Composition** | Strong | Contained object dies with container | House HAS-A Room |
| **Aggregation** | Weak | Contained object can exist independently | Department HAS-A Teacher |

---

## Composition (Strong Ownership)

> **Composition**: When the contained object CANNOT exist independently of the container. The container is responsible for creating and destroying the contained object.

### Characteristics of Composition
- Container **creates** the contained object
- Container **owns** the contained object completely
- When container is destroyed, contained object is **also destroyed**
- Contained object has no independent existence

### Real-World Examples
- House HAS Rooms (Rooms can't exist without House)
- Car HAS Engine (Engine is created within Car)
- Body HAS Heart (Heart can't exist independently)

### Complete Example: House and Rooms

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class Room                              // Line 4: Room class (contained)
{
private:
    char* name;                         // Line 7: Room name
public:
    Room(char* name)                    // Line 9: Parameterized constructor
    {
        this->name = new char[strlen(name) + 1];    // Line 11: Allocate memory
        strcpy_s(this->name, strlen(name) + 1, name); // Line 12: Copy name
    }
    Room()                              // Line 14: Default constructor
    {
        name = NULL;                    // Line 16: Initialize to NULL
    }
    void setName(char* name)            // Line 18: Setter
    {
        this->name = new char[strlen(name) + 1];
        strcpy_s(this->name, strlen(name) + 1, name);
    }
    char* getName()                     // Line 23: Getter
    {
        return name;
    }
    ~Room()                             // Line 27: Destructor
    {
        cout << name << " is destroyed" << endl;  // Line 29: Print message
        delete name;                    // Line 30: Free memory
    }
};

class House                             // Line 34: House class (container)
{
private:
    Room* rooms;                        // Line 37: Array of Room objects (OWNS them)
    char* name;                         // Line 38: House name
    char* address;                      // Line 39: House address
    int no_rooms;                       // Line 40: Number of rooms
public:
    House(char* name, char* address, int no_rooms)  // Line 42: Constructor
    {
        // Copy house name
        this->name = new char[strlen(name) + 1];
        strcpy_s(this->name, strlen(name) + 1, name);
        
        // Copy address
        this->address = new char[strlen(address) + 1];
        strcpy_s(this->address, strlen(address) + 1, address);
        
        this->no_rooms = no_rooms;
        
        // CREATE rooms inside House - Composition!
        this->rooms = new Room[no_rooms];           // Line 54: House creates rooms!
        
        char rname[30];
        for (int i = 0; i < no_rooms; i++)          // Line 57: Initialize each room
        {
            cout << "enter room name" << endl;
            cin.getline(rname, 30);
            this->rooms[i].setName(rname);
        }
    }
    
    char* getName()                     // Line 65: Getter
    {
        return name;
    }
    
    char* getAddress()                  // Line 70: Getter
    {
        return address;
    }
    
    void showRooms()                    // Line 75: Display all rooms
    {
        for (int i = 0; i < no_rooms; i++)
        {
            cout << rooms[i].getName() << endl;
        }
    }
    
    ~House()                            // Line 83: Destructor
    {
        cout << endl << "House destroyed" << endl;
        delete name;
        delete address;
        delete[] rooms;                 // Line 88: House DESTROYS rooms!
                                        // Rooms cannot exist without House
    }
};

int main()                              // Line 93: Main function
{
    char name[] = "Samrat Mansion";
    char address[] = "Juhu,Mumbai";
    House* house = new House(name, address, 4);  // Line 97: Create house with 4 rooms
    
    cout << house->getName();
    cout << house->getAddress();
    cout << "house has following rooms" << endl;
    house->showRooms();
    
    cout << "Lets renovate the house" << endl;
    delete house;                       // Line 105: Delete house → rooms also deleted
    
    return 0;
}
```

### Execution Flow

```
┌────────────────────────────────────────────────────────────────┐
│                COMPOSITION EXECUTION FLOW                       │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│   House* house = new House(..., 4);                             │
│   │                                                             │
│   └──► House constructor:                                       │
│        ├── Allocate name, address                               │
│        └── rooms = new Room[4];  ← Creates 4 Room objects      │
│            ├── Prompts for room names                           │
│            └── Rooms are OWNED by House                         │
│                                                                 │
│   delete house;                                                 │
│   │                                                             │
│   └──► House destructor:                                        │
│        ├── cout << "House destroyed"                            │
│        ├── delete name, address                                 │
│        └── delete[] rooms;  ← Destroys all Room objects!       │
│            ├── ~Room() for room 1  → "room1 is destroyed"      │
│            ├── ~Room() for room 2  → "room2 is destroyed"      │
│            ├── ~Room() for room 3  → "room3 is destroyed"      │
│            └── ~Room() for room 4  → "room4 is destroyed"      │
│                                                                 │
│   KEY: When House dies, all Rooms die with it!                  │
│        Rooms have NO independent existence.                     │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

---

## Aggregation (Weak Association)

> **Aggregation**: When the contained object CAN exist independently of the container. The container only holds a reference/pointer to the object.

### Characteristics of Aggregation
- Container does **NOT create** the contained object
- Container only **uses** the contained object
- When container is destroyed, contained object **survives**
- Contained object has **independent existence**

### Real-World Examples
- Department HAS Teachers (Teachers exist independently)
- Company HAS Employees (Employees can join other companies)
- Library HAS Books (Books can be moved)

### Complete Example: Department and Teacher

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class Teacher                           // Line 4: Teacher class (can exist independently)
{
private:
    char* name;                         // Line 7: Teacher name
    int age;                            // Line 8: Teacher age
public:
    Teacher(char* name, int age)        // Line 10: Parameterized constructor
    {
        this->name = new char[strlen(name) + 1];
        strcpy_s(this->name, strlen(name) + 1, name);
        this->age = age;
    }
    
    char* getName()                     // Line 17: Getter
    {
        return name;
    }
    
    int getAge()                        // Line 22: Getter
    {
        return age;
    }
    
    ~Teacher()                          // Line 27: Destructor
    {
        cout << "Teacher is removed" << endl;
        delete name;
    }
    
    void work()                         // Line 33: Work method
    {
        cout << "Teacher is working" << endl;
    }
};

class Department                        // Line 39: Department class (container)
{
private:
    Teacher* teacher;                   // Line 42: POINTER to Teacher (doesn't own!)
    char* dname;                        // Line 43: Department name
public:
    Department(char* dname)             // Line 45: Constructor
    {
        teacher = NULL;                 // Line 47: Initially no teacher
        this->dname = new char[strlen(dname) + 1];
        strcpy_s(this->dname, strlen(dname) + 1, dname);
    }
    
    char* getDname()                    // Line 53: Getter
    {
        return dname;
    }
    
    void addTeacher(Teacher& ref)       // Line 58: Add teacher by REFERENCE
    {                                   // Department doesn't CREATE teacher!
        this->teacher = &ref;           // Line 60: Just stores pointer
    }
    
    void perform()                      // Line 63: Use teacher
    {
        cout << "inside " << dname << " ";
        teacher->work();
    }
    
    ~Department()                       // Line 69: Destructor
    {
        cout << endl << "Department is getting close" << endl;
        delete dname;
        // NOTE: teacher is NOT deleted here!
        // Teacher exists independently!
    }
};

int main()                              // Line 78: Main function
{
    char ch[] = "Abc";
    char ch1[] = "Science Department";
    char ch2[] = "Maths Department";
    
    Department* scienceDepartment = new Department(ch1);  // Line 84: Create department
    Department* mathsDepartment = new Department(ch2);    // Line 85: Create department
    
    cout << scienceDepartment->getDname() << endl;
    cout << mathsDepartment->getDname() << endl;
    
    Teacher t1(ch, 35);                 // Line 90: Create teacher INDEPENDENTLY
    cout << t1.getName() << "\t" << t1.getAge() << endl;
    
    // Teacher joins Maths department
    mathsDepartment->addTeacher(t1);    // Line 94: Add teacher to maths
    mathsDepartment->perform();
    
    cout << "Lets close the maths department" << endl;
    delete mathsDepartment;             // Line 98: Maths department closed
    
    // Teacher STILL EXISTS! Can join another department
    cout << t1.getName() << " still exists and can join some other department" << endl;
    
    // Teacher joins Science department
    scienceDepartment->addTeacher(t1);  // Line 103: Add same teacher to science
    scienceDepartment->perform();
    
    // ..... after some time ......
    delete scienceDepartment;           // Line 107: Science department closed
    
    cout << t1.getName() << " still exists and can join some other department" << endl;
    
    return 0;                           // Line 111: t1 destructor called at end of main()
}
```

**Output:**
```
Science Department
Maths Department
Abc     35
inside Maths Department Teacher is working
Lets close the maths department

Department is getting close
Abc still exists and can join some other department
inside Science Department Teacher is working

Department is getting close
Abc still exists and can join some other department
Teacher is removed
```

### Execution Flow

```
┌────────────────────────────────────────────────────────────────┐
│                AGGREGATION EXECUTION FLOW                       │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Teacher t1("Abc", 35);                                        │
│   │                                                             │
│   └──► Teacher created INDEPENDENTLY                            │
│        (Not inside any department)                              │
│                                                                 │
│   mathsDepartment->addTeacher(t1);                              │
│   │                                                             │
│   └──► Department just STORES POINTER to t1                     │
│        (Doesn't create or own the teacher)                      │
│                                                                 │
│   delete mathsDepartment;                                       │
│   │                                                             │
│   └──► Department destructor:                                   │
│        ├── cout << "Department is getting close"                │
│        ├── delete dname                                         │
│        └── teacher pointer NOT deleted!                         │
│                                                                 │
│   t1 STILL EXISTS after maths department closes!                │
│   │                                                             │
│   └──► Can be added to science department                       │
│                                                                 │
│   End of main():                                                │
│   │                                                             │
│   └──► t1 goes out of scope                                     │
│        └── ~Teacher() called → "Teacher is removed"             │
│                                                                 │
│   KEY: Department's death does NOT kill Teacher!                │
│        Teacher has INDEPENDENT existence.                       │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

---

## Constructor/Destructor Order with Composition

### Combining Is-A and Has-A

```cpp
#include<iostream>                      // Line 1: Include iostream
using namespace std;                    // Line 2: Use standard namespace

class Animal                            // Line 4: Base class (for Is-A)
{
public:
    Animal(int k)                       // Line 7: Parameterized constructor
    {
        cout << "Animal param constr" << endl;
    }
    ~Animal()                           // Line 11: Destructor
    {
        cout << "inside Animal destructor" << endl;
    }
};

class Tail                              // Line 17: Tail class (for Has-A)
{
public:
    Tail(char ch)                       // Line 20: Parameterized constructor
    {
        cout << "in Tail param constr" << endl;
    }
    ~Tail()                             // Line 24: Destructor
    {
        cout << "in Tail destructor" << endl;
    }
};

class Dog : public Animal               // Line 30: Dog IS-A Animal
{
private:
    Tail w;                             // Line 33: Dog HAS-A Tail (COMPOSITION)
public:
    Dog() : w('A'), Animal(10)          // Line 35: Constructor
    {                                   // Note: Order in initializer list doesn't matter!
        cout << "Dog def constr" << endl;
    }
    ~Dog()                              // Line 39: Destructor
    {
        cout << "inside Dog destructor" << endl;
    }
};

int main()                              // Line 45: Main function
{
    Dog d;                              // Line 47: Create Dog object
    return 0;
}
```

**Output:**
```
Animal param constr     ← Base class first (Is-A)
in Tail param constr    ← Member object second (Has-A)
Dog def constr          ← Derived class last
inside Dog destructor   ← Derived class first
in Tail destructor      ← Member object second
inside Animal destructor← Base class last
```

### Order Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│          CONSTRUCTION/DESTRUCTION ORDER                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   CONSTRUCTION ORDER:                                            │
│   1. Base class constructor   (Animal)   ← IS-A                 │
│   2. Member object constructor (Tail)    ← HAS-A                 │
│   3. Derived class constructor (Dog)                            │
│                                                                  │
│   DESTRUCTION ORDER (reverse):                                   │
│   1. Derived class destructor (Dog)                             │
│   2. Member object destructor (Tail)     ← HAS-A                │
│   3. Base class destructor   (Animal)    ← IS-A                 │
│                                                                  │
│   NOTE: Even though initializer list says w('A'), Animal(10)    │
│         Animal is constructed FIRST because:                    │
│         - Base constructors always come before members          │
│         - Initializer list order doesn't change actual order    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Comparison: Aggregation vs Composition

```
┌────────────────────────────────────────────────────────────────────────────┐
│              AGGREGATION vs COMPOSITION COMPARISON                          │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   Feature          │ Composition           │ Aggregation                   │
│   ─────────────────┼───────────────────────┼─────────────────────────────  │
│   Ownership        │ Strong (owns)         │ Weak (uses)                   │
│   Lifetime         │ Same as container     │ Independent                   │
│   Creation         │ Container creates     │ Created outside               │
│   Destruction      │ Container destroys    │ Survives container death      │
│   Symbol (UML)     │ Filled diamond ◆      │ Empty diamond ◇               │
│   Memory           │ Often embedded object │ Pointer/reference             │
│                                                                             │
│   CODE EXAMPLE:                                                             │
│                                                                             │
│   // COMPOSITION                    // AGGREGATION                          │
│   class House {                     class Department {                      │
│       Room rooms[4];  ← embedded        Teacher* teacher; ← pointer        │
│   public:                           public:                                 │
│       House() {                         void addTeacher(Teacher& t) {       │
│           // rooms created here              teacher = &t; // just store   │
│       }                                 }                                   │
│       ~House() {                        ~Department() {                     │
│           // rooms destroyed               // teacher NOT deleted          │
│       }                                 }                                   │
│   };                                };                                      │
│                                                                             │
│   REAL-WORLD ANALOGIES:                                                     │
│                                                                             │
│   Composition:                      Aggregation:                            │
│   • Car HAS Engine                  • Department HAS Teacher                │
│   • House HAS Rooms                 • Company HAS Employees                 │
│   • Computer HAS CPU                • Team HAS Players                      │
│   • Body HAS Heart                  • Library HAS Books                     │
│                                                                             │
└────────────────────────────────────────────────────────────────────────────┘
```

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────────────┐
│          AGGREGATION & COMPOSITION - KEY POINTS                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. HAS-A RELATIONSHIP:                                          │
│     • One class contains another as a member                    │
│     • Alternative to inheritance for code reuse                 │
│     • Two types: Composition and Aggregation                    │
│                                                                  │
│  2. COMPOSITION (Strong):                                        │
│     • Container CREATES and OWNS contained object               │
│     • Contained object dies with container                      │
│     • Usually embedded object or dynamically allocated          │
│     • Example: House HAS Rooms                                  │
│                                                                  │
│  3. AGGREGATION (Weak):                                          │
│     • Container only HOLDS REFERENCE/POINTER                    │
│     • Contained object exists independently                     │
│     • Object created and destroyed separately                   │
│     • Example: Department HAS Teachers                          │
│                                                                  │
│  4. CONSTRUCTOR ORDER:                                           │
│     1st: Base class constructor (Is-A)                          │
│     2nd: Member object constructors (Has-A)                     │
│     3rd: Derived class constructor                              │
│                                                                  │
│  5. DESTRUCTOR ORDER:                                            │
│     Reverse of construction order                               │
│                                                                  │
│  6. WHEN TO USE:                                                 │
│     • Inheritance: When "Child IS-A Parent" makes sense         │
│     • Composition: When "Container OWNS Part"                   │
│     • Aggregation: When "Container USES Part"                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Previous: [Method Redefinition](37_Method_Redefinition.md)*
*Next: [Upcasting and Binding](39_Upcasting_and_Binding.md)*
