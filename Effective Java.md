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
  
### 9. try-with-resources 优先于try-finally
* 主要针对资源的关闭
* try-finally 当有多个资源需要关闭时，就不太适用，另外存在异常覆盖
  ```java
    static String fisrtLineOfFile(String path) throws IOException{
        BufferedReader br = new BufferdReader(new FileReader(path));
        try{
            return br.readLine();
        }finally{
            br.close();
        }
    }
  ```
* try-with-resources 需要实现AutoCloseable接口
  ```java
      // the best way to cloase resources
      static String fisrtLineOfFile(String path) throws IOException{
          try(BufferedReader br = new new BufferdReader(new FileReader(path))){
              return br.readLine();
          }
      }

      // try-with-resources on multiple resources
      static void copy(String src, String dst) throws IOException{
          try(InputStream in = new FileInputStream(src); OutputStream out = new FileOutputStream(dst){
                  byte[] buf = new byte[BUFFER_SIZE];
                  int n;
                  while ((n == in.read(buf)) >= 0)
                      out.write(buf, 0, n);
              }
          }
      }
   ```
   

***
## 二、对于所有对象都通用的方法

### 10、覆盖equals时请遵守通用约定
* 与默认equals方法等效
  * 类的每个实例本质上都是唯一的
  * 类没必要提供“逻辑相等”的测试功能
  * 超类已经覆盖了equals，超类的行为对于这个类也是合适的
  * 类是私有的，或者是包级私有的，可以确定它的equals方法永远不会被调用
* 覆盖equals的需求：
  * 类具有自己独特的“逻辑相等”，而且超累还没有覆盖equals（值类）
  * 如 Integer、String
* 覆盖equals方法必须遵守的通用约定
  * 自反性。x.equals(x)返回true
  * 对称性。x.equals(y) == y.equals(x)
  * 传递性。x.equals(y),y.equals(z) x.equals(z)
  * 一致性。多次操作结果一样
  * x.equals(null) return false
* 实现高质量equals方法
  * step1：使用 == 操作符检查“参数是否为这个对象的引用”，如果是，返回true；
  * step2：使用instanceof操作符检查“参数是否为正确的类型”，如果不是，返回false；
  * step3：把参数转换成正确的类型（强制转换）；
  * step4：对于该类中的每个“关键”域，检查参数中的域是否是域该对象中对应的域相匹配。
* 对于float和double类型，使用Float.Compare(float, float),Double.compare(double, double),因为存在Float.NaN、-0.0f以及类似的double常量
    ```java
        public final class PhoneNumber{
            private final short areaCode, prefix, lineNumber;

            public PhoneNumber(int areaCode, int prefix, int lineNumer){
                this.areaCode = areaCode;
                this.prefix = prefix;
                this.lineNumber = lineNumber;
            }

            private static short rangeCheck(int val, int max, String arg){
                if (val < 0 || val > max)
                    throw new IlleagArgumentException(arg + ":" + val);
                return (short) val;
            }

            @Override public boolean equals(Object o){
                // step1
                if(o == this) 
                    return true;
                
                // step2
                if (!(o instanceof PhoneNumber)) 
                    return false;

                // step3
                PhoneNumber pn = (PhoneNumber) o;
                
                // step 4 先比较最有可能不一致 或开销较小的
                return pn.lineNum == lineNumber && pb.prefix == prefix && pn.areaCode == areaCode;
            }
        }
    ```
* 注意：
  * 覆盖equals时总要覆盖hashCode。
  * 不要企图让equals方法过于智能。
  * 不要将equals声明中的Object对象替换为其它的类型。（重载会导致没有覆盖Object类的方法）
* 不要轻易覆盖equals方法
  
### 11、覆盖equals时总要覆盖hashCode
* 在每个覆盖了equals方法的类中，都必须覆盖hashCode方法。
* 相等的对象必须有相等的散列码（hashCode）---如hashMap的应用需求
* hashCode不同，代表两个对象没有任何共同之处。
* AutoValue框架
    ```java
        // Typical hashCode method
        @Override
        public int hashCode(){
            int result = Short.hashCode(area);
            result = 31 * result + Short.hashCode(prefix);
            result = 31 * result + Short.hashCode(lineNumber);
            return result;
        }
    ```
* 不要试图从散列码计算中排除一个对象的关键域来提高性能

### 12、始终要覆盖toString
* toString方法应该返回对象中包含的值得关注的信息
* 每一个可实例化的类中覆盖Object的toString实现

### 13、谨慎地覆盖clone

