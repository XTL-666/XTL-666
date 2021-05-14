- 👋 Hi, I’m @XTL-666
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
XTL-666/XTL-666 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
# 并查集

并查集（Union-find Sets）是一种非常精巧而实用的数据结构，它主要用于处理一些*不相交集合*的合并问题。一些常见的用途有求连通子图、求最小生成树的 Kruskal 算法和求最近公共祖先（Least Common Ancestors, LCA）等。



它有三个哈希表

元素值表:主要用来包装值对应的元素

儿子-父亲表  判断是否为同一集合

rankMap 每个父所对应的rank大小



我们先定义包装值所对应的元素

```java
public class UnionFind {

   public static class Element<V> {
       public V value;
       public Element(V value) {
           this.value = value;
       }
   }
}
```



去定义这三个表

把表装入已有的元素

```java
public static class UionFindSet<V> {
    public HashMap<V,Element<V>> elementMap;
    public HashMap<Element<V>,Element<V>> fatherMap;
    public HashMap<Element<V>,Integer> rankMap;
    
    public UionFindSet(List<V> list) {
    	elementMap = new HashMap<>();
    	fatherMap = new HashMap<>();
    	rankMap = new HashMap<>();
    	
    	for(V value : List) {
    	    Element<V> element = new Element<V>(value);
            elementMap.put(value,element);
            fatherMap.put(element,element);
            rankMap.put(element,1);
    	}
    }
}
```

它有三个重要的函数

findHead： 找到头节点，并且更新它，让路径让的每一个元素都指向头节点

isSameSet ：判断是不是一个集合直接判断是不是头节点就行了

Uion接下来讲

在findHead中我们需要维护一个栈用来记录沿途的元素，以后便于之后连接上他们的头节点

```java
        private Element<V> findHead(Element<V> element) {
            Stack<Element<V>> path = new Stack<>();
            while(element != fatherMap.get(element)) {
                path.push(element);
                element = fatherMap.get(element);
            }
            while(!path.isEmpty()) {
                fatehrMap.put(path.pop(),element);
            }
            return element;
        }
```



接下俩就很简单了 

我们让同一集合元素拥有一个头节点，那么我们只需要确定他们的头节点是否相同就可以了

```
        public boolean isSameSet(V a,V b) {
            if(elementMap.containsKey(a) && elementMap.containsKey(b)) {
                return findHead(elementMap.get(a)) == findHead(elementMap.get(b));
            }
            return false;
        }
```

接下来是Uion操作

在查询是否为统一集合与合并集合之前，我们应该确保他们的值对应元素集合表中的元素



```java
        public void union(V a,V b) {
            if(elementMap.containsKey(a)&&elementMap.containsKey(b)){
                Element<V> aF =  findHead(elementMap.get(a));
                Element<V> bF =  findHead(elementMap.get(b));
                
                //如果aF == bF,我们就可以确定他们的头节点是同一个，那么属于同一集合
                
                if(aF != bF){
                    Element<V> big = rankMap.get(aF) > rankMap.get(bF) ? aF : bF;
                    Element<V> small = big == aF ? bF : aF; //将aF与bF中较小的赋予small
                    fatherMap.put(small,big);
                    rankMap.put(big,rankMap.get(aF) + rankMap.get(bF));
                    rankMap.remove(small);
                } 
            }
        }
```

