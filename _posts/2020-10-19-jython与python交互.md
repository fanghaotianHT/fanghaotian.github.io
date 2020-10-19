---
layout:     post                    
title:      java与python交互             
subtitle:   jython
date:       2020-10-19           
author:     HT                     
header-img: img/code.jpg    
catalog: true                       
tags:                               
    - java 
    - python
---

# Java与python交互

## java中调用python

一、Jython安装

Jython：Jython是在JVM上实现的Python，由Java编写。Jython将Python源码编译成JVM字节码，由JVM执行对应的字节码。因此能很好的与JVM集成，比如利用JVM的垃圾回收和JIT，直接导入并调用JVM上其他语言编写的库和函数。

在Jython的官方下载页面我们可以看到如下描述（详见：https://www.jython.org/download）

![](D:/Typora/图片/1.png)

上面我们看到在Jython官网提供了2个jar包，一个用于安装Jython，执行Python程序。另一个`jython-standalone.jar`又有什么用途呢？
实际上，当我们需要在Java中调用Python程序时，除了直接使用Java的Runtime调用，还可以直接使用Jython的API进行调用，而且通过Jython API可以直接调用Python程序中的指定函数或者对象方法，粒度更加精细。

下载`jython-standalone.jar`，并在项目中导入这个jar包

二、使用jython在java中调用python

jythonutils工具类

```
import java.util.*;

import org.apache.poi.ss.formula.functions.T;
import org.python.core.*;
import org.python.util.PythonInterpreter;
/**
 * @ClassName:  JythonUtils
 * @Description:TODO(jython 工具类)
 * 注意：
 */
public class JythonUtils_class {

    /**
     * @Title: jythonInit
     * @Description: TODO(初始化jython)
     * @param: @return
     * @return: PythonInterpreter
     * @throws
     */
    public static PythonInterpreter jythonInit(){
        //初始化site 配置
        Properties props = new Properties();
        props.put("python.home", ""); //python Lib 或 jython Lib,根据系统中该文件目录路径
        props.put("python.console.encoding", "UTF-8");
        props.put("python.security.respectJavaAccessibility", "false");
        props.put("python.import.site", "false");
        Properties preprops = System.getProperties();
        PythonInterpreter.initialize(preprops, props, new String[0]);
        //创建PythonInterpreter 对象
        PythonInterpreter interp = new PythonInterpreter();
        return interp;
    }

    /**
     * @Title: loadPythonFile
     * @Description: TODO(加载python 源码文件，)
     * @param: @param interp
     * @param: @param filePath ，比如：F:\\jpython_jar\\jpythonTest\\pythonTest.py  或/testpython/test.py
     * @param: @return
     * @return: PythonInterpreter
     * @throws
     */
    public static PythonInterpreter loadPythonFile(PythonInterpreter interp, String filePath){
        interp.execfile(filePath);
        return interp;
    }

    /**
     * @Title: loadPythonFunc
     * @Description: TODO(加载python 源码文件中的某个方法)
     * @param: @param interp
     * @param: @param functionName
     * @param: @return
     * @return: PyFunction
     * @throws
     */
    /*
    public static PyFunction loadPythonFunc(PythonInterpreter interp, String functionName){

        //加载方法
        PyFunction func = (PyFunction) interp.get(functionName,PyFunction.class);
        return func;
    }

    */


    /**
     * @Title: execFunc
     * @Description: TODO(执行无参方法,返回PyObject)
     * @param: @param func
     * @return: PyObject
     * @throws
     */
    /*
    public static PyObject execFunc(PyFunction func){
        PyObject pyobj = func.__call__();
        return pyobj;
    }

     */

    /**
     * @Title: execFuncToString
     * @Description: TODO(执行无参方法,返回一个字符串)
     * @param: @param func
     * @param: @return
     * @return: String
     * @throws
     */
    /*
    public static String execFuncToString(PyFunction func){
        PyObject pyobj = execFunc(func);
        return (String) pyobj.__tojava__(String.class);
    }

     */

    /**
     * @Title: execFuncToString
     * @Description: TODO(执行有参方法,返回一个字符串)
     * @param: @param func
     * @param: @param paramName ，参数名
     * @param: @return
     * @return: String
     * @throws
     */
    public static String execFuncToString2(PyObject pyObj,String func, String paramName){
        PyObject pyobj = pyObj.invoke(func,new PyString(paramName));
        return (String) pyobj.__tojava__(String.class);
    }

    /**
     * @Title: execFuncToInteger
     * @Description: TODO(执行无参方法,返回一个Integer)
     * @param: @param func
     * @param: @return
     * @return: Integer
     * @throws
     */
    public static Integer execFuncToInteger(PyObject pyCls,String func){
        PyObject pyobj = pyCls.invoke(func);
        return (Integer) pyobj.__tojava__(Integer.class);
    }
    /**
     * @Title: execFuncToByParamDouble
     * @Description: TODO(执行无参方法,返回一个Integer)
     * @param: @param func
     * @param: @return
     * @return: Double
     * @throws
     */
    public static Double execFuncToByParamDouble(PyObject pyCls,String func,Double param){
        PyObject pyobj = pyCls.invoke(func,new PyFloat(param));
        return (Double) pyobj.__tojava__(Double.class);
    }

    /**
     * @Title: execFuncToByParamBoolean
     * @Description: TODO(执行无参方法,返回一个Integer)
     * @param: @param func
     * @param: @return
     * @return: Boolean
     * @throws
     */
    public static Boolean execFuncToByParamBoolean(PyObject pyCls,String func,Boolean param){
        PyObject pyobj = pyCls.invoke(func,new PyBoolean(param));
        return (Boolean) pyobj.__tojava__(Boolean.class);
    }



    /**
     * @Title: execFuncToList
     * @Description: TODO(执行无参方法,返回一个List)
     * @param: @param func
     * @param: @return
     * @return: List<T>
     * @throws
     */
    public static List<T> execFuncToList(PyObject pyCls,String func){
        PyObject pyobj = pyCls.invoke(func);
        return (List<T>) pyobj.__tojava__(List.class);
    }
    /**
     * @Title: execFuncToMap
     * @Description: TODO(执行有参方法,返回一个List<T>)
     * @param: @param func
     * @param: @return
     * @return: List
     * @throws
     */

    public static List<Double> execFuncToByParamsList(PyObject pyCls,String func, List<Double> paramsList){
        PyObject pyobj = pyCls.invoke(func,new PyList(paramsList));
        return (List<Double>) pyobj.__tojava__(List.class);
    }
    /**
     * @Title: execFuncToByParamsList2
     * @Description: TODO(执行有参方法,传入Map,返回一个List<T>)
     * @param: @param func
     * @param: @return
     * @return: List<T>
     * @throws
     */
    public static List<T> execFuncToByParamsList2(PyObject pyCls,String func, Map<Object, PyObject>  paramsListMap) {
        PyObject pyobj = pyCls.invoke(func, new PyStringMap(paramsListMap));
        return (List<T>) pyobj.__tojava__(List.class);
    }

    /**
     * @Title: execFuncToMap
     * @Description: TODO(执行无参方法,返回一个Map<String, Object>)
     * @param: @param func
     * @param: @return
     * @return: Map<String,Object>
     * @throws
     */

    public static Map<String, Object> execFuncToMap(PyObject pyCls,String func){
        PyObject pyobj = pyCls.invoke(func);
        return (Map<String, Object>) pyobj.__tojava__(Map.class);
    }


    /**
     * @Title: execFuncToMap
     * @Description: TODO(执行有参方法,返回一个Map<String, Object>)
     * @param: @param func
     * @param: @return
     * @return: Map<String,Object>
     * @throws
     */

    public static Map<Object, PyObject>  execFuncToMap2(PyObject pyCls,String func,Map<Object, PyObject>  paramsMap){
        PyObject pyobj = pyCls.invoke(func,new PyStringMap(paramsMap));
        return (Map<Object, PyObject>) pyobj.__tojava__(Map.class);
    }


    public static void main(String[] args){
        PythonInterpreter interp  = jythonInit();
        //文件名
        String filePath = "python\\pythonclass.py";
        interp = loadPythonFile(interp, filePath);

        PyObject pyClass = interp.get("jython");
        PyObject pyCls = pyClass.__call__();

        //执行无参方法，返回integer
        String functionName1 = "Print_int";
        int resultInt = execFuncToInteger(pyCls,functionName1);
        System.out.println(resultInt);
        //执行有参方法，返回String
        String functionName2 = "Print_string";
        String paramName = "name";
        String resultStr2 = execFuncToString2(pyCls, functionName2,paramName);
        System.out.println(resultStr2);

        //执行无参方法，返回List
        String functionName3 = "Print_list";
        //PyFunction func2 = loadPythonFunc(interp, functionName3);
        List resultList = execFuncToList(pyCls,functionName3);
        System.out.println(resultList);
        for (Object x:resultList) {
            System.out.print(x+" ");
        }
        System.out.println();
        //执行无参方法，返回Map
        String functionName4 = "Print_map";
        //PyFunction func5 = loadPythonFunc(interp, functionName5);
        Map<String, Object> resultMap = execFuncToMap(pyCls,functionName4);
        System.out.println(resultMap);
        for (Object o1:resultMap.values()) {
            System.out.print(o1 + " ");
        }
        System.out.println();


        //执行有参方法，返回List
        String functionName5 = "Print_List";
        //PyFunction func3 = loadPythonFunc(interp, functionName3);
        List<Double> paras = new ArrayList<>();
        paras.add(0.1);
        paras.add(0.2);
        paras.add(0.3);
        paras.add(0.4);
        List<Double> resultList2 = execFuncToByParamsList(pyCls,functionName5 , paras);
        System.out.println(resultList2);
        for (Object o2:resultList2) {
            System.out.print(o2+" ");
        }
        System.out.println();

        /*
        //执行有参方法，返回Map
        String functionName6 = "Print_Map";
        //PyFunction func4 = loadPythonFunc(interp, functionName4);
        Map<Object, PyObject>  paramsMap = new HashMap<Object, PyObject>();
        paramsMap.put("Google", new PyInteger(1));
        paramsMap.put("Runoob", new PyInteger(2));
        paramsMap.put("Taobao", new PyInteger(3));
        paramsMap.put("Zhihu",  new PyInteger(4));
        Map<Object, PyObject> resultMap2 = execFuncToMap2(pyCls,functionName6, paramsMap);
        System.out.println(resultMap2);
        for (Object o3:resultMap2.values()) {
            System.out.print(o3+" ");
        }
        */

        //执行有参方法，传入Map,返回List
        String functionName7 = "Print_Map2";
        //PyFunction func4 = loadPythonFunc(interp, functionName4);
        Map<String, List<Double>>  M = new HashMap<>();
        List<Double> L = new ArrayList();
        L.add(0.0001);
        M.put("Google", L);
        M.put("Runoob", L);
        M.put("Taobao", L);
        Map<Object,PyObject>paramsMap2 =new HashMap<>();
        for (String key :M.keySet()){
            paramsMap2.put(key,new PyList(M.get(key)));
        }
        List<T>  resultList3 = execFuncToByParamsList2(pyCls,functionName7, paramsMap2);
        System.out.println(resultList3);
        for (Object o4:resultList3) {
            System.out.print(o4+" ");
        }
        System.out.println();
        //执行有参方法，返回Double
        String functionName8 = "Print_double";
        //PyFunction func4 = loadPythonFunc(interp, functionName4);
        double paramDouble=0.1;
        double resultDouble = execFuncToByParamDouble(pyCls,functionName8, paramDouble);
        System.out.println(resultDouble);
        //执行有参方法，返回Boolean
        String functionName9 = "Print_boolean";
        //PyFunction func4 = loadPythonFunc(interp, functionName4);
        boolean paramBoolean=true;
        boolean resultBoolean = execFuncToByParamBoolean(pyCls,functionName9, paramBoolean);
        System.out.println(resultBoolean);
    }
}
```

python文件

```
def Print_int():
    a=3
    return a
def Print_string(str):
    s=str+"fht"
    return s

def Print_list():
    y=[1,2,3,4,5,6,7]
    return y


def Print_List(l):
    print(l)
    l.append(0.5)
    l.append(0.8)
    return l

def Print_Map(m1):
    print(m1)
    m1['weibo']=5
    print(m1)
    print(type(m1))
    return m1
def Print_Map2(m2):
    print(m2)
    li = []
    for key  in m2.items():
        li.append(key)
    print(li)
    return li

def Print_map():
    dict = {'Name': 'Zara', 'Age': 7, 'Class': 'First'}
    return dict
def Print_double(d):
    print(d)
    d=d+0.254
    return d

def Print_boolean(b):
    print(b)
    b=False
    return b
```

上述所用方法可以我在Java中直接调用python函数并传递参数获取返回值

稍加修改可以调用python类中的方法

```
import java.util.*;

import org.apache.poi.ss.formula.functions.T;
import org.python.core.*;
import org.python.util.PythonInterpreter;
/**
 * @ClassName:  JythonUtils
 * @Description:TODO(jython 工具类)
 * 注意：
 */
public class JythonUtils_class {

    /**
     * @Title: jythonInit
     * @Description: TODO(初始化jython)
     * @param: @return
     * @return: PythonInterpreter
     * @throws
     */
    public static PythonInterpreter jythonInit(){
        //初始化site 配置
        Properties props = new Properties();
        props.put("python.home", ""); //python Lib 或 jython Lib,根据系统中该文件目录路径
        props.put("python.console.encoding", "UTF-8");
        props.put("python.security.respectJavaAccessibility", "false");
        props.put("python.import.site", "false");
        Properties preprops = System.getProperties();
        PythonInterpreter.initialize(preprops, props, new String[0]);
        //创建PythonInterpreter 对象
        PythonInterpreter interp = new PythonInterpreter();
        return interp;
    }

    /**
     * @Title: loadPythonFile
     * @Description: TODO(加载python 源码文件，)
     * @param: @param interp
     * @param: @param filePath ，比如：F:\\jpython_jar\\jpythonTest\\pythonTest.py  或/testpython/test.py
     * @param: @return
     * @return: PythonInterpreter
     * @throws
     */
    public static PythonInterpreter loadPythonFile(PythonInterpreter interp, String filePath){
        interp.execfile(filePath);
        return interp;
    }

    /**
     * @Title: loadPythonFunc
     * @Description: TODO(加载python 源码文件中的某个方法)
     * @param: @param interp
     * @param: @param functionName
     * @param: @return
     * @return: PyFunction
     * @throws
     */
    /*
    public static PyFunction loadPythonFunc(PythonInterpreter interp, String functionName){

        //加载方法
        PyFunction func = (PyFunction) interp.get(functionName,PyFunction.class);
        return func;
    }

    */


    /**
     * @Title: execFunc
     * @Description: TODO(执行无参方法,返回PyObject)
     * @param: @param func
     * @return: PyObject
     * @throws
     */
    /*
    public static PyObject execFunc(PyFunction func){
        PyObject pyobj = func.__call__();
        return pyobj;
    }

     */

    /**
     * @Title: execFuncToString
     * @Description: TODO(执行无参方法,返回一个字符串)
     * @param: @param func
     * @param: @return
     * @return: String
     * @throws
     */
    /*
    public static String execFuncToString(PyFunction func){
        PyObject pyobj = execFunc(func);
        return (String) pyobj.__tojava__(String.class);
    }

     */

    /**
     * @Title: execFuncToString
     * @Description: TODO(执行有参方法,返回一个字符串)
     * @param: @param func
     * @param: @param paramName ，参数名
     * @param: @return
     * @return: String
     * @throws
     */
    public static String execFuncToString2(PyObject pyObj,String func, String paramName){
        PyObject pyobj = pyObj.invoke(func,new PyString(paramName));
        return (String) pyobj.__tojava__(String.class);
    }

    /**
     * @Title: execFuncToInteger
     * @Description: TODO(执行无参方法,返回一个Integer)
     * @param: @param func
     * @param: @return
     * @return: Integer
     * @throws
     */
    public static Integer execFuncToInteger(PyObject pyCls,String func){
        PyObject pyobj = pyCls.invoke(func);
        return (Integer) pyobj.__tojava__(Integer.class);
    }
    /**
     * @Title: execFuncToByParamDouble
     * @Description: TODO(执行无参方法,返回一个Integer)
     * @param: @param func
     * @param: @return
     * @return: Double
     * @throws
     */
    public static Double execFuncToByParamDouble(PyObject pyCls,String func,Double param){
        PyObject pyobj = pyCls.invoke(func,new PyFloat(param));
        return (Double) pyobj.__tojava__(Double.class);
    }

    /**
     * @Title: execFuncToByParamBoolean
     * @Description: TODO(执行无参方法,返回一个Integer)
     * @param: @param func
     * @param: @return
     * @return: Boolean
     * @throws
     */
    public static Boolean execFuncToByParamBoolean(PyObject pyCls,String func,Boolean param){
        PyObject pyobj = pyCls.invoke(func,new PyBoolean(param));
        return (Boolean) pyobj.__tojava__(Boolean.class);
    }



    /**
     * @Title: execFuncToList
     * @Description: TODO(执行无参方法,返回一个List)
     * @param: @param func
     * @param: @return
     * @return: List<T>
     * @throws
     */
    public static List<T> execFuncToList(PyObject pyCls,String func){
        PyObject pyobj = pyCls.invoke(func);
        return (List<T>) pyobj.__tojava__(List.class);
    }
    /**
     * @Title: execFuncToMap
     * @Description: TODO(执行有参方法,返回一个List<T>)
     * @param: @param func
     * @param: @return
     * @return: List
     * @throws
     */

    public static List<Double> execFuncToByParamsList(PyObject pyCls,String func, List<Double> paramsList){
        PyObject pyobj = pyCls.invoke(func,new PyList(paramsList));
        return (List<Double>) pyobj.__tojava__(List.class);
    }
    /**
     * @Title: execFuncToByParamsList2
     * @Description: TODO(执行有参方法,传入Map,返回一个List<T>)
     * @param: @param func
     * @param: @return
     * @return: List<T>
     * @throws
     */
    public static List<T> execFuncToByParamsList2(PyObject pyCls,String func, Map<Object, PyObject>  paramsListMap) {
        PyObject pyobj = pyCls.invoke(func, new PyStringMap(paramsListMap));
        return (List<T>) pyobj.__tojava__(List.class);
    }

    /**
     * @Title: execFuncToMap
     * @Description: TODO(执行无参方法,返回一个Map<String, Object>)
     * @param: @param func
     * @param: @return
     * @return: Map<String,Object>
     * @throws
     */

    public static Map<String, Object> execFuncToMap(PyObject pyCls,String func){
        PyObject pyobj = pyCls.invoke(func);
        return (Map<String, Object>) pyobj.__tojava__(Map.class);
    }


    /**
     * @Title: execFuncToMap
     * @Description: TODO(执行有参方法,返回一个Map<String, Object>)
     * @param: @param func
     * @param: @return
     * @return: Map<String,Object>
     * @throws
     */

    public static Map<Object, PyObject>  execFuncToMap2(PyObject pyCls,String func,Map<Object, PyObject>  paramsMap){
        PyObject pyobj = pyCls.invoke(func,new PyStringMap(paramsMap));
        return (Map<Object, PyObject>) pyobj.__tojava__(Map.class);
    }


    public static void main(String[] args){
        PythonInterpreter interp  = jythonInit();
        //文件名
        String filePath = "python\\pythonclass.py";
        interp = loadPythonFile(interp, filePath);

        PyObject pyClass = interp.get("jython");
        PyObject pyCls = pyClass.__call__();

        //执行无参方法，返回integer
        String functionName1 = "Print_int";
        int resultInt = execFuncToInteger(pyCls,functionName1);
        System.out.println(resultInt);
        //执行有参方法，返回String
        String functionName2 = "Print_string";
        String paramName = "name";
        String resultStr2 = execFuncToString2(pyCls, functionName2,paramName);
        System.out.println(resultStr2);

        //执行无参方法，返回List
        String functionName3 = "Print_list";
        //PyFunction func2 = loadPythonFunc(interp, functionName3);
        List resultList = execFuncToList(pyCls,functionName3);
        System.out.println(resultList);
        for (Object x:resultList) {
            System.out.print(x+" ");
        }
        System.out.println();
        //执行无参方法，返回Map
        String functionName4 = "Print_map";
        //PyFunction func5 = loadPythonFunc(interp, functionName5);
        Map<String, Object> resultMap = execFuncToMap(pyCls,functionName4);
        System.out.println(resultMap);
        for (Object o1:resultMap.values()) {
            System.out.print(o1 + " ");
        }
        System.out.println();


        //执行有参方法，返回List
        String functionName5 = "Print_List";
        //PyFunction func3 = loadPythonFunc(interp, functionName3);
        List<Double> paras = new ArrayList<>();
        paras.add(0.1);
        paras.add(0.2);
        paras.add(0.3);
        paras.add(0.4);
        List<Double> resultList2 = execFuncToByParamsList(pyCls,functionName5 , paras);
        System.out.println(resultList2);
        for (Object o2:resultList2) {
            System.out.print(o2+" ");
        }
        System.out.println();

        /*
        //执行有参方法，返回Map
        String functionName6 = "Print_Map";
        //PyFunction func4 = loadPythonFunc(interp, functionName4);
        Map<Object, PyObject>  paramsMap = new HashMap<Object, PyObject>();
        paramsMap.put("Google", new PyInteger(1));
        paramsMap.put("Runoob", new PyInteger(2));
        paramsMap.put("Taobao", new PyInteger(3));
        paramsMap.put("Zhihu",  new PyInteger(4));
        Map<Object, PyObject> resultMap2 = execFuncToMap2(pyCls,functionName6, paramsMap);
        System.out.println(resultMap2);
        for (Object o3:resultMap2.values()) {
            System.out.print(o3+" ");
        }
        */

        //执行有参方法，传入Map,返回List
        String functionName7 = "Print_Map2";
        //PyFunction func4 = loadPythonFunc(interp, functionName4);
        Map<String, List<Double>>  M = new HashMap<>();
        List<Double> L = new ArrayList();
        L.add(0.0001);
        M.put("Google", L);
        M.put("Runoob", L);
        M.put("Taobao", L);
        Map<Object,PyObject>paramsMap2 =new HashMap<>();
        for (String key :M.keySet()){
            paramsMap2.put(key,new PyList(M.get(key)));
        }
        List<T>  resultList3 = execFuncToByParamsList2(pyCls,functionName7, paramsMap2);
        System.out.println(resultList3);
        for (Object o4:resultList3) {
            System.out.print(o4+" ");
        }
        System.out.println();
        //执行有参方法，返回Double
        String functionName8 = "Print_double";
        //PyFunction func4 = loadPythonFunc(interp, functionName4);
        double paramDouble=0.1;
        double resultDouble = execFuncToByParamDouble(pyCls,functionName8, paramDouble);
        System.out.println(resultDouble);
        //执行有参方法，返回Boolean
        String functionName9 = "Print_boolean";
        //PyFunction func4 = loadPythonFunc(interp, functionName4);
        boolean paramBoolean=true;
        boolean resultBoolean = execFuncToByParamBoolean(pyCls,functionName9, paramBoolean);
        System.out.println(resultBoolean);
    }
}
```

调用类中的方法，类名为jython

```
PyObject pyClass = interp.get("jython");
PyObject pyCls = pyClass.__call__();
```

## python中调用java类

### 第一步：

创建一个java类，如hellojava

```
public class hellojython {
    public int javatopython(){
        return 1;
    }
    public static void main(String[] args) {
        System.out.println("Hello Jython!");
    }
}
```

记住hellojavaclass文件路径（C:\Users\Administrator\IdeaProjects\hellojython\out\production\hellojython）

### 第二步：

创建jython项目

1）在JetBrains IntelliJ IDEA中新建项目，左侧选择“Python”，右侧“New...”-->“Add Local”，找到刚安装的Jython安装目录下的jython.exe文件D:\Program_Files\jython2.7.0\bin\jython.exe，“OK”：

“Next”，“Next”，输入项目名称“HelloJython”，“Finish”；

![img](D:\Typora\图片\20170609091037836)

2）在项目目录下新建目录“src”，右击该目录，“Mark Directory As”-->“Sources Root”：

3）工具栏“File”-->“Project Structure”，左侧选择“Libraries”，点击绿色加号按钮，找到希望调用的HelloJython.class文件所在的文件夹（C:\Users\Administrator\IdeaProjects\hellojython\out\production\hellojython），“OK”；

4）在“src”文件夹下创建Python文件“main.py”：

```
import hellojython

hello_jython = hellojython()
x=hello_jython.javatopython()
print(x)
```

运行main.py



## java调用python遇到的坑

目前最新的jython版本为2.7.2，如果在Python中引入了第三方库如torch则会出现错误

![](D:/Typora/图片/2-1603108883687.png)

因此在使用此方法时py文件中不能引入不支持python2的第三方库

只能等待开发者发布jython3的版本

![](D:/Typora/图片/3.jpg)

jython3正在开发中，尚未发布
