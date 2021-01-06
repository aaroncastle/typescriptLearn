### **为什么**学习TypeScript

   >我的个人理由：因为有webstorm的强大加持，我不存在下面的烦恼。只是为了更好的使用js用面向对象的方式开发。（这是TS带来的福利)。
   > 
   > 对于大部分人来说，因为js的开发只用了两周时间，在设计时并没有为大型工程化开发做考虑
   > 
> 因此，这个弱类型的语言成了后期开发的烦恼。因为整个开发周期中几乎一半的时间都用在排错。（因为解释性语言的报错总是在运行时，而非在开发时）
> 
### **安装ts**

```shell
yarn global add typescript # 全局安装是为了加tsc编译器，可以全局调用tsc
```

ts会做出三种假设：

> 1，**假设运行环境是DOM**
>
> 2，**假设编译的目标代码是ES3**
>
> 3，**如果代码中没有使用模块化语句(import 、export)，便认为该代码是全局执行**

两种方法更改以上假设：

> 1，使用tsc命令行加上选项参数；
>
> 2，使用ts配置文件，更改编译选项；（常用）

### ts 的配置文件

> 1，在项目下创建tsconfig.json
>
> 2，用tsc生成tsconfig.json

```shell
tsc --init
```

```json
{
  "compilerOptions": {
    "target" : "es2016",//编译生成的js语法类型
    "lib": ["ES2016"],// 默认生成的js的环境。如果使用node环境，没有Node的选项，要安装@types/node。@types是一个ts官方的类型库，其中了很多的对js代码的类型描述
    "module": "CommonJS",//编译目标使用的模块化标准
    "outDir": "./dist"
  },
  "include": ["./src"] // 要编译目标的文件夹
  //"files": ["./src/index.ts"] // 只编译某个文件夹的某个文件
}
```

```shell
yarn add -D @types/node
tsc # tsc后面如果跟上文件名就会自动忽略配置文件生成dist目录
```

### 使用第三方库简化编译流程

`ts-node`：将ts代码在内存中完成编译，同时依据tsconfig.json完成运行

```shell
yarn add ts-node
ts-node src/index.js #会依据tsconfig.json 编译运行，但不生成dist目录
```

`nodemon` ：监控文件的变化，在文件改变时可以在脚本中自动运行。

```shell
yarn add -D nodemon
nodemon --exec ts-node src/index.ts # 可以将此命令写在package.json文件的脚本中，让其自动运行
```

示例如下：

> --watch src // nodemon监控的文件路径
>
> -e ts // 监控的扩展名为ts

```json
{
  "dependencies": {
    "ts-node": "^9.0.0",
    "typescript": "^4.0.3"
  },
  "devDependencies": {
    "@types/node": "^14.11.1",
    "nodemon": "^2.0.4"
  },
  "scripts": {
    "dev" : "nodemon --watch src  -e ts --exec ts-node src/index.ts"
  }
}
```

****

### 基本类型约束

> TS是一个可选的静态类型系统

#### 如何进行类型约束

> 仅需要在**变量、函数的参数、函数的返回值**的位置加上`:类型`
>
> TS**在很多场景中可以进行类型推导**，有类型推导的场景不需要写类型约束
>
> **什么时候有类型推导?** : 如果变量下面有三个点（VsCode会显示）推导类型为`any`  ts 不会对 `any`类型进行类型检查

```typescript
let search:string = 'search'
// @ts-ignore
console.log(search)
function sub (a:number, b: number): number {
    return  a + b;
}
sub(3, 4)
let age;
```

**约束的基本类型**

`string` / `number` / `boolean`/

`array` 要细化到数组的每一项的值的类型

```typescript
let nums: number[]; //数字类型的数组
let num: Array<number> = [1,2,3]
```

`object` 对象约束到每一项太难，因为对象的属性十分复杂，但还是有对对象约束的场景

```typescript
function printValue (obj:object) {
  const vals = Object.values(obj)
}
```

`null`和`undefind` 可以赋值给任意类型

```typescript
let num: number = null; // 这种类型极容易造成代码出错，所以在tsconfig.json中可以进行配置
```

严格的空类型检查，这样就不能将null和undefind赋值给其他的类型，只能赋值给自身。

```json
{
  "compilerOptions": {
    "strictNullChecks": true  
  }
}
```

----

**联合类型：**多种类型选择其一

```typescript
let name: string | undefind;
if(typeof name === 'string'){
  //在这里就可以对类型进行准确的类型推导，这种行为就是类型保护
}
```

**类型保护** 当对某个变量进行类型判断后，在判断的语句块中，便可以对类型进行确定。

> `typeof`可以触发简单的基本类型保护

`void` 类型：通常用来约束类型的返回值，表示该函数没有任何返回。

`never`类型：通常用于约束函数的返回值，表示函数永远不可能结束。

字面量类型：非常强力的约束

```typescript
let sex: '男' | '女'; // 该变量永远只可能取值为"男"或"女"
let arr: []; // arr永远只能取值为空数组
let  user : {
  name:string
  age: number
} // user对象必须要有一个字符串类型的name属性和一个数字类型的age
```



元祖类型（Tuple）：一个固定长度的数组，数组的每一项的类型确定

```typescript
let aar : [string,number] // aar数组长度只能为2，第[0]项只能为string类型，第[1]项只能为number类型
```



`any`类型：`any`类型可以绕过类型检查



