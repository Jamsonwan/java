# Effective Java

## 一. 创建和销毁对象

### 1.  用静态工厂方法代替构造器
  * 静态工厂方法的实现方式
     
    ```java

        public static Boolean valueOf(boolean b){
            return b ? Boolean.TRUE : Boolean.FALSE;
        }

        //确保每次返回一个新的实例
        public static Myclass newInstance();

        public static Myclass getInstance();
        
    ```
  * 优势
    * 静态工厂方法与构造器不同的第一个优势在于，静态工厂方法有名称。
    * 静态工厂方法与构造器不同的第二个优势在于，不必在每次调用它们的时候都创建一个新对象。
    * 静态工厂方法与构造器不同的第三个优势在于，可以返回原返回类型的任何子类型的对象。
    * 静态工厂方法与构造器不同的第四个优势在于，所返回的对象的类可以随着每次调用而发生变化，这取决于静态工厂方法的参数值。
    * 静态工厂方法与构造器不同的第五个优势在于，方法返回的对象所属的类，在编写包含该静态工厂方法的类时可以不存在。
  * 缺点
    * 类如果不含有共有或受保护的构造器，就不能被子类化。
    * 程序员很难发现它们。

### 2. 遇到多个构造器参数时要考虑使用构建器

* 有部分参数的必须的，而有些参数是可选的情况。
* 解决方法
  * 重叠构造器
  * JavaBeans模式，利用setter()和getter()方法
  * Builder模式--->通常是不可变的对象
* Builder模式

  ```java
  
  public class NutritionFacts{
      private final int servingSize;
      private final int servings;
      private final int calories;
      private final int fat;
      private final int sodium;
      private final int carbohydrate;
      
      public static class Builder{
          //Required parameters
          private final int servingSize;
          private final int servings;
          
          // Optional parameters
          private int calories = 0;
          private int fat = 0;
          private int sodium = 0;
          private int carbohydrate = 0;
          
          public Builder(int servingSize, int servings){
              this.servingSize = servingSize;
              this.servings = servings;
          }
          
          public Builder calories(int val){
              calories = val;
              return this;
          }
          
          public Builder fat(int val){
              fat = val;
              return this;
          }
          
          public Builder sodium(int val){
              sodium = val;
              return this;
          }
          
          public Builder carbohydrate(int val){
              carbohydrate = val;
              return this;
          }
          
          public NutritionFacts build(){
              return new NutritionFacts(this);
          }
      }
      
      private NutritionFacts(Builder builder){
          servingSize = builder.servingSize;
          servings = builder.servings;
          calories = builder.calories;
          fat = builder.fat;
          sodium = builder.sodium;
          carbohydrate = builder.carbohydrate;
      }
  }
  
  //Call 流式API
  NutritionFacts cacaCola = new NutritionFacts.Builder(240, 8).calories(100).sodium(53).carbohydrate(27).build();
  
  ```

  **如果类的构造器或者静态工厂中具有多个参数，设计这种类时，Builder模式就是一种不错的选择**

### 3. 用私有构造器或者枚举类型强化Singleton属性

* Singleton是指仅仅被实例化一次的类。
* 实现方法
  ```java
    // Singleton with public final field
    public class Elvis{
        public static final Elvis INSTANCE = new Elvis();
        private Elvis(){} // 构造器私有化

        public void leaveTheBuilding(){}
    }

    // Singleton with static factory
    public class Elvis{
        // 私有
        private static final Elvis INSTANCE = new ELVIS();
        private Elvis(){};

        public static Elvis getInstance(){
            return INSTANCE;
        }
        public void leaveTheBuilding(){}
        
        // 保证反序列化
        public Object readResolve(){
            return INSTANCE;
        }
    }

    // Enum singleton -- the preferred approach --与公有域方法相似
    public enum Elvis{
        INSTANCE;
        public void leaveTheBuilding(){}
    }
  ```
  **单元素的枚举类型经常成为实现Singleton的最佳方法。**

### 4. 通过私有构造器强化不可实例化的能力

* 针对只包含静态方法和静态域的类
  
### 5.优先考虑依赖注入来引用资源
* 静态工具类和Singleton类不适合于需要引用底层资源的类。
* 当创建一个新的实例时，将资源传递到构造器中，这就是依赖注入的一种形式。
* 依赖注入的对象资源具有不可变性。
  ```java
    public class SpellChecker{
        private final Lexicon dictionary;

        public SpellChecker(Lexicon dictionary){
            this.dictionary = Objects.requiredNonNull(dictionary);
        }
    }    
  ```
### 6.避免创建不必要的对象
* 重用不可变对象。
* 要优先使用基本类型而不是装箱基本类型，要当心无意识的自动装箱。
### 7.消除过期的对象引用
* 内存泄漏：无意识的对象保持
* 栈的pop()方法
  ```java
    public Object pop(){
        if (size == 0)
            throw new EmptyStackException();
        return elements[--size]; // size之上的对象仍然存在
    }

    public Object pop(){
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null; // Eliminate obsolete reference 清除过期引用
        return result;
    }
  ```
* 只要类是自己管理内存，就应当警惕内存泄漏问题。
* 常见的来源：缓存、监听器和其他回调。

### 8.避免使用终结方法和清除方法
* 终结方法（finalizer）通常是不可预测的，也是很危险的，一般情况下是不必要的。
* 清除方法（cleaner）没有终结方法那么危险，但仍然是不可预测、运行缓慢，一般情况下也是不必要的。
* finalizer和cleaner的缺点在于不能保证被及时执行。
  



