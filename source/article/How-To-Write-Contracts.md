>本文翻译自：<https://eosio.github.io/eos/group__contractdev.html>

# How-To-Write-Contracts
Introduction to writing contracts for EOS.IO. More...

# 如何编写合约
关于如何为EOS.IO编写合约的介绍。[更多](#detail)

[toc]

## Modules
[Database API](#)
 	APIs that store and retreive data on the blockchain EOS.IO organizes data according to the following broad structure: 
 	在区块链存储和检索数据的接口。
 
 [Math API](#)
 	Defines common math functions. 
 	定义了通用的数学函数。
 
 [Message API](#)
 	Define API for querying message properties. 
 	定义了查询消息属性的接口。
 
 [Console API](#)
 	Enables applications to log/print text messages. 
 	开启应用程序记录或打印文本信息。
 
 [Token API](#)
 	Defines the ABI for interfacing with standard-compatible token messages and database tables. 
 	定义了标准兼容的 token 消息和数据库表之间交互的ABI（应用系统二进制接口）
 
 [Builtin Types](#)
 	Specifies typedefs and aliases. 
 	类型定义和别名。
 
## Detailed Description

<a name="detail"></a>
## 详细描述

### Background

EOS.IO contracts (aka applications) are deployed to a blockchain as pre-compiled Web Assembly (aka WASM). WASM is compiled from C/C++ using LLVM and clang, which means that you will require knowledge of C/C++ in order to develop your blockchain applications. While it is possible to develop in C, we strongly recommend that all developers use the EOS.IO C++ API which provides much stronger type safety and is generally easier to read.

### 背景

EOS.IO 合约（亦称应用程序）作为预编译的web程序集（WASM）被部署到区块链。WASM 使用 LLVM 和 clang 从 C/C++ 编译，这意味着你需要有 C/C++ 知识来开发区块链应用程序。虽然可以用 C 来开发，但是，我们强烈建议所有的开发者使用 EOS.IO C++ 接口。因为 C++ 接口提供了更加健壮的类型安全支持并且通常更容易阅读。

### Application Structure

EOS.IO applications are designed around event (aka message) handlers that respond to user actions. For example, a user might transfer tokens to another user. This event can be processed and potentially rejected by the sender, the receiver, and the currency application itself.

As an application developer you get to decide what actions users can take and which handlers may or must be called in response to those events.

### 应用程序结构

EOS.IO 应用程序被设计为使用事件（亦称消息）处理函数来响应用户行为。例如，一个用户可以转移 token（代币） 给另一个用户。这个事件可以被发送者或接受者或者应用程序自身处理或拒绝。

作为应用程序开发者，你应该决定哪些行为是用户可以触发的，哪个事件处理函数可以或必须被调用来响应那些事件。

#### Entry Points

EOS.IO applications have a apply which is like main in traditional applications:

```
extern "C" {
   void init();
   void apply( uint64_t code, uint64_t action );
}
```

main is give the arguments code and action which uniquely identify every event in the system. For example, code could be a currency contract and action could be transfer. This event (code,action) may be passed to several contracts including the sender and receiver. It is up to your application to figure out what to do in response to such an event.

init is another entry point that is called once immediately after loading the code. It is where you should perform one-time initialization of state.

#### 入口点
EOS.IO 应用程序有一个 apply 方法，等价于传统应用程序的 main 方法。

```
extern "C" {
   void init();
   void apply( uint64_t code, uint64_t action );
}
```

main 方法接收输入参数 code 和 action。action 在整个系统中必须是独一无二的。例如，code 可能是一个货币合约，action 可能是一个转让动作。这个事件（code,action)可以被传递给包含发送者和接受者的若干合约。应用程序来决定如何响应这个事件。

init 方法是另一个入口点。它只在加载代码的时候会被调用一次。你应该在这个方法中执行一次性的状态初始化工作。


#### Example Apply Entry Handler

Generally speaking, you should use your entry handler to dispatch events to functions that implement the majority of your logic and optionally reject events that your contract is unable or unwilling to accept.

```
extern "C" {
   void apply( uint64_t code, uint64_t action ) {
      if( code == N(currency) ) {
         if( action == N(transfer) ) 
            currency::apply_currency_transfer( currentMessage< currency::Transfer >() );
      } else {
         assert( false, "rejecting unexpected event" );
      }
   }
}
```

#### Apply 入口处理函数实例

一般而言，你应该使用入口处理函数来分发事件到实现主业务逻辑的函数。当合约不能或者不想接收该事件时，也在入口处理函数拒绝。

```
extern "C" {
   void apply( uint64_t code, uint64_t action ) {
      if( code == N(currency) ) {
         if( action == N(transfer) ) 
            currency::apply_currency_transfer( currentMessage< currency::Transfer >() );
      } else {
         assert( false, "rejecting unexpected event" );
      }
   }
}
```

>**Note**
>When defining your entry points it is required that they are placed in an extern "C" code block so that c++ name mangling does not get applied to the function.

>**注意**
>当定义你自己的入口函数时，需要把它们放在  extern "C" 代码块中。以便 C++ 名称管理器不会把它们当成普通函数。

--
>本文译者：[gumoon](https://github.com/gumoon)

