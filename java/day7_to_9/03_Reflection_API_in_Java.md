# Reflection API in Java

Reflection is a powerful feature in Java that allows a program to inspect and modify its own structure (classes, interfaces, fields, and methods) at runtime. It essentially allows the "Execution Environment" to look at itself in the mirror.

## 1. What is Reflection?

Normally, you know the class types, methods, and fields at compile time. Reflection allows you to:
1.  **Introspect**: Analyze a class at runtime (list its methods, fields, constructors).
2.  **Dynamic Invocation**: Create objects and call methods of a class whose name you only know at runtime (e.g., provided as a String).
3.  **Bypass Encapsulation**: Access private fields and methods (useful for testing frameworks).

**Analogy**: 
- **Compile-time**: You know you are driving a "Car". You use `car.drive()`.
- **Reflection**: You are given an "Object". You ask "What are you?". It says "I am a Car". You ask "What can you do?". It says "I can drive". You say "Then drive!".

---

## 2. Introspection (Getting Class Details)

The entry point for reflection is the `java.lang.Class` class.

#### Code Example: `ReflectionDemo1.java`

```java
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class ReflectionDemo1 {
    public static void main(String args[]) {
        Class c = null;
        try {
            // 1. Load the class dynamically using its name (String)
            // Passes "java.lang.String" or any class name as args[0]
            c = Class.forName(args[0]); 
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }

        // 2. Get and display all methods
        Method methods[] = c.getDeclaredMethods();
        for(Method m : methods) {
            System.out.println(m);
        }

        // 3. Get and display all constructors
        Constructor constructors[] = c.getDeclaredConstructors();
        for(Constructor con : constructors) {
            System.out.println(con);
        }

        // 4. Get and display all fields
        Field fields[] = c.getDeclaredFields();
        for(Field f : fields) {
            System.out.println(f);
        }
    }
}
```

---

## 3. Dynamic Instantiation

You can create an instance of a class knowing only its name string.

#### Code Example: `ReflectionDemo2.java`

```java
import java.util.*;

// Classes with similar method signatures but no common header interface for this demo
class One { void disp1() { System.out.println("in disp1"); } }
class Two { void disp2() { System.out.println("in disp2"); } }
class Three { void disp3() { System.out.println("in disp3"); } }

public class ReflectionDemo2 {
    
    // Factory method to create object from String
    static Object createObject(String className) {
        Object object = null;
        try {
            Class classDefinition = Class.forName(className);
            // newInstance() calls the zero-argument constructor
            object = classDefinition.newInstance(); 
        } 
        catch (InstantiationException e) { System.out.println("No default constructor: " + e); } 
        catch (IllegalAccessException e) { System.out.println("Constructor not accessible: " + e); }
        catch (ClassNotFoundException e) { System.out.println("Class not found: " + e); }
        return object;
    }

    public static void main(String args[]) {
        try {
            Scanner sc = new Scanner(System.in);
            System.out.println("Enter class name (One, Two, Three):");
            String str = sc.next();
            
            // Runtime Object Creation
            Object ob = createObject(str);
            
            // Checking type and casting to invoke methods
            if(ob instanceof One) {
                ((One)(ob)).disp1();
            } else if(ob instanceof Two) {
                ((Two)(ob)).disp2();
            } else if(ob instanceof Three) {
                ((Three)(ob)).disp3();
            }
        } catch(Exception ee) {
            System.out.println(ee);
        }
    }
}
```

---

## 4. Accessing Private Members

Reflection can break encapsulation by making private members accessible using `setAccessible(true)`.

#### Accessing Private Methods (`ReflectionDemo3.java`)

```java
import java.lang.reflect.Method;

class Sample {
    private void disp1() {
        System.out.println("in disp1 (Private)");
    }
}

public class ReflectionDemo3 {
    public static void main(String args[]) {
        try {
            Class c = Class.forName("Sample");
            
            // Get private method 'disp1'
            Method m = c.getDeclaredMethod("disp1", null);
            
            Sample s = new Sample();
            
            // CRITICAL: Allow access to private method
            m.setAccessible(true); 
            
            // Invoke the private method on object 's'
            m.invoke(s, null); 
        } catch(Exception ee) {
            System.out.println(ee);
        }
    }
}
```

#### Modifying Private Fields (`ReflectionDemo4.java`)

```java
import java.lang.reflect.Field;

class Sample {
    private int num1 = 200; // Private field
    public int getNum1() { return num1; }
}

public class ReflectionDemo4 {
    public static void main(String args[]) {
        try {
            Class c = Class.forName("Sample");
            Sample s = new Sample();
            
            System.out.println("Before modifying: " + s.getNum1()); // 200
            
            // Get the private field
            Field value = c.getDeclaredField("num1");
            
            // Make it accessible
            value.setAccessible(true); 
            
            // Modify the value
            value.set(s, 120); 
            
            System.out.println("After modifying: " + s.getNum1()); // 120
        } catch(Exception ee) {
            System.out.println(ee);
        }
    }
}
```

---

## 5. Bridge Methods

Bridge methods are synthetic methods generated by the compiler to maintain type safety when using Generics co-variant return types.

#### Code Example: `BridgeDemo.java`

```java
import java.lang.reflect.*;

class Base {
    Object disp() { 
        System.out.println("in base disp"); 
        return null; 
    }
}

class Sub extends Base {
    // Co-variant return type (String is subclass of Object)
    @Override
    String disp() { 
        System.out.println("in sub disp"); 
        return null; 
    }
}

public class BridgeDemo {
    public static void main(String args[]) {
        Sub obj = new Sub();
        
        try {
            Class c = obj.getClass();
            Method methods[] = c.getDeclaredMethods();
            
            for(Method m : methods) {
                System.out.println("Method: " + m.getName() + " | Return Type: " + m.getReturnType());
                System.out.println("Is Bridge? " + m.isBridge());
            }
        } catch(Exception ee) {
            ee.printStackTrace();
        }
    }
}
```

**Output Explanation:**
You will see **TWO** `disp` methods in `Sub` class via reflection:
1.  `String disp()` - The one you wrote. `isBridge() = false`.
2.  `Object disp()` - The **Bridge Method** generated by compiler. `isBridge() = true`.
    - It overrides `Base.disp()` and internally calls `Sub.disp()` (the String one).
    - This ensures that code expecting `Base.disp()` (returning Object) still works with `Sub`.
