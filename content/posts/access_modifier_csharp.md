---
title: "Access Modifier in csharp"
date: 2019-11-22T23:29:14+05:45
author: false
draft: false
lightgallery: true
date: 2020-08-25T07:46:58+05:45
featuredImage: /images/access_modifier_csharp/preview.png
featuredImagePreview: /images/access_modifier_csharp/preview.png
---

> *Access Modifier is the keyword which decide who can access  resource or object in a C# program. We can control the access level  of class, method , variables in a program using a access modifier.*

There are six Access Modifiers in C# which have different features and which give different levels of access to the object(class/method/variable) with which it is used.


### Public Access Modifier
The objects(methods/properties) with public access modifiers can be accessed from anywhere in a project. There is no accessibility restriction for public access modifiers. 

```CSharp
using System;
namespace CSharp_Access_Modifiers
{
    class PublicMsg
    {
        // Accessible anywhere in program.
        public string msg = "This is public message."; 
    }

    class Program
    {
        static void Main()
        {
            var pMsg = new PublicMsg();
            Console.WriteLine(pMsg.msg);
        }
    }
}
```

```
Output:
This is public message.
```

In the above program  we declared *msg* variable *public* and accessed from another class *Program*. As public access modifier has no restriction in its access level it can be accessed from anywhere in program i.e from another class, another assembly/namespace.

### Internal Access Modifier
The objects with internal access modifiers can be accessed from anywhere within the same assembly or namespaces. 

```csharp
using System;
namespace CSharp_Access_Modifiers
{
    class InternalMsg
    {
        internal string msg = "This is internal message."; 
    }

    class Program
    {
        static void Main()
        {
            var interMsg = new InternalMsg();
            Console.WriteLine(interMsg.msg);
        }
    }
}
```

```
Output:
This is internal message.
```

In the above code we declared msg variable internal and used in another class Program. As the object with internal access modifier can be accessed in anywhere in same assembly we got output without any error.

### Protected  Access Modifier
Protected access modifier limits the accessibility of objects within the class in which it is created or in the derived class.  

```csharp
using System;
namespace CSharp_Access_Modifiers
{
    class ProtectedMsg
    {
        protected string msg = "This is protected message."; 
    }

    class Program:ProtectedMsg
    {
        static void Main()
        {
            var proMsg = new Program();
            // Accessible from derived class.
            Console.WriteLine(proMsg.msg); 
        }
    }
}
```
```
Output:
This is protected message.
```
In the above code, we derived *Program* class from *ProtectedMsg* class and as *msg* variable is within its access level we got output.
Object with protected access modifier can also be accessed from another assembly from a derived class.

```csharp
using System;
namespace CSharp_Access_Modifiers
{
     public class ProtectedMsg
    {
        protected  string msg = "This is protected  message.";
        static void Main()
        {
        }
    }
}

// Another Assembly
using CSharp_Access_Modifiers;
using System;
namespace AnotherAssembly
{
    class Program : ProtectedMsg
    {
        static void Main()
        {
            var anotherMsg = new Program();
            Console.WriteLine(anotherMsg.msg);
        }
    }
}
```

```
Output:
This is protected  message.
```


### Private Access Modifier
The objects with private access modifiers can only be accessed by code in the same class so It is not accessible outside the class they are created. It is used to ensure *encapsulation* in the program i.e process of hiding sensitive items from the user. 

```csharp
using System;
namespace CSharp_Access_Modifiers
{
    class PrivateMsg
    {
        private string msg = "This is private message.";
        static void Main()
        {
            var prMsg = new PrivateMsg(); 
            //accessing the variable with in the class 
            Console.WriteLine(prMsg.msg);
        }
    }
}
```

```
Output:
This is private message.
```
Private access modifier has the least access level among all the six access modifier.

*In C# access modifiers can be combined like combining protected and internal forms protected internal access modifier and likewise combining private protected form private protected access modifier.*

### Protected Internal Access Modifier
The objects with protected internal access modifiers can only be accessed within the same assembly or from within derived classes from another assembly.

```csharp
using System;
namespace CSharp_Access_Modifiers
{
    public class ProtectedInternalMsg
    {
        protected internal string msg = "This is protected internal message.";
    }
}

// Another assembly 
using CSharp_Access_Modifiers;
using System;
namespace AnotherAssembly
{
    class Program : ProtectedInternalMsg
    {
        static void Main()
        {
            var anotherMsg = new Program();
            // Accessible from derived class in another assembly
            Console.WriteLine(anotherMsg.msg); 
        }
    }
}
```

```
Output:
This is protected internal message.
```

### Private protected Access Modifier
The objects with private protected access modifiers can only be accessed from the same class or the derived class.

```csharp
using System;
namespace CSharp_Access_Modifiers
{
    class PrivateProtectedMsg
    {
        private protected  string msg = "This is private protected  message.";
    }

    class Program:PrivateProtectedMsg
    {
        static void Main()
        {
            var prproMsg = new Program();
            // accessible from base class or derived class only.
            Console.WriteLine(prproMsg.msg); 

        }
    }
}
```

```
Output:
This is private protected  message.
```
Private Protected access modifier allows an object to be accessed from derived class in the same assembly. In the above code, *msg* variable is accessed from *Program* class which is derived from the *PrivateProtectedMsg* class.

### Default Access Modifier
When there is no access modifier is set , then a default access modifier will be used. Default Access Modifier is different for the  
Default access modifier for *namespace, enum, interface* is **public** and their access modifier can't be changed or set. The default access level for  *class, struct, method, variable* is **private**.  

Here is the summary of accessibility level of the Access Modifier in C#:

| Access Modifier    | Entire Program | Base Class | Current Assembly | Derived Class in another assembly | Derived Class in current assembly |
|--------------------|----------------|------------|------------------|-----------------------------------|-----------------------------------|
| Public             | Yes            | Yes        | Yes              | Yes                               | Yes                               |
| Protected          | No             | Yes        | No               | Yes                               | Yes                               |
| Internal           | No             | Yes        | Yes              | No                                | Yes                               |
| Protected Internal | No             | Yes        | Yes              | Yes                               | Yes                               |
| Private            | No             | Yes        | No               | No                                | No                                |
| Private Protected  | No             | Yes        | No               | No                                | Yes                               |

So , that was all about the access modifiers in C#. 

