# Singleton

Provides one point of access to a class, a global identifier. Encapsulate the attributes in only one class.

## Motivation:
    Avoid unnecessary use of multiple class when you need to have only one global class.

### Understanding the code:
    A basic singleton class look like this:
        class Singleton {   
            private static Singleton instance;
            private String data;    
            
            //Only made private because we need to create a static method to access the class
            private Singleton(String data){
                 this.data = data;
            }
            
            public static Singleton getInstance(String data){
                if(instance == null){
                    instance = new Singleton(data)
                }
                return instance
            }
        }

#### This example may be a problem when we're dealing with multi-threading. Does not prevent two threads accessing the same instance at the same time.
    //To solve this we need to wrap our if statement with a syncronized block
    class Singleton {   
            private static Singleton instance;
            private String data;    
            
            //Only made private because we need to create a static method to access the class
            private Singleton(String data){
                 this.data = data;
            }

            public static Singleton getInstance(String data){
                synchronized (Singleton.class){
                    if(instance == null){
                        instance = new Singleton(data)
                    }
                }
                return instance
            }
        }

#### We solved the access issue, but now every thread needs to wait even the instance has been created.
    //To solve this, can be used a double-checking on the synchronized block and limit the rare case of construction of the instance
    class Singleton {   
            private static Singleton instance;
            private String data;    
            
            //Only made private because we need to create a static method to access the class
            private Singleton(String data){
                 this.data = data;
            }

            public static Singleton getInstance(String data){
                if(instance == null){
                    synchronized (Singleton.class){
                        if(instance == null){
                            instance = new Singleton(data)
                        }
                    }
                }
                return instance
            }
        }
#### We're not done yet, if two threads to create the instance of the same time, they are not now going to be able to wait to one or another create the instance, because they're creating at the same time.
    //To solve this, can be simply resolved by adding the 'volatile' keyword to the instance.
    /**
        Volatile field:
            - Mutual Exclusion: Only one thread can execute a block of code at a given time, or do updates on shared data
            - Visibility: Changes done by one thread are visible to other threads
        Volatile variables are not cached ! They are read directly from main memory.
    */
    class Singleton {   
            private static volatile Singleton instance;
            private String data;    
            
            //Only made private because we need to create a static method to access the class
            private Singleton(String data){
                 this.data = data;
            }

            public static Singleton getInstance(String data){
                if(instance == null){
                    synchronized (Singleton.class){
                        if(instance == null){
                            instance = new Singleton(data)
                        }
                    }
                }
                return instance
            }
        }
#### Combining the volatile keyword with the synchronized keyword, allow us to provide less overhead on methods or blocks, and both reads and writes will be done directly from and to the main memory, ensuring mutual exclusion and visibility. Applying to the context, only one thread will be responsable to create a new instance but all the others threads will be able to read. 