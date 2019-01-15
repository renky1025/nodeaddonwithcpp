# how to write c++ addon for node js

# 1. install node-gyp first
` npm install g node-gyp`

# 2. create Gyp configuration
` touch binding.gyp `

contents in binding.gyp

{
   "targets": [
       {
           "target_name": "addon",
           "sources": [ "addon.cc" ]
       }
   ]
}


"Target_name" - with its help you can connect the compiled C++ code
"Sources" - array of *.cc file names that contain C++ code

# 3. write c++ file

#include <node.h>
#include <iostream>

using namespace v8;
using namespace std;

void HelloWorld(const FunctionCallbackInfo<Value>& args) {
   cout << "Hello, world!" << endl;
}

void AddNumbers(const FunctionCallbackInfo<Value>& args) {
   Isolate* isolate = args.GetIsolate();
   double valueToSum = args[0]->NumberValue();
   double result = 0;
   int sumCount = args[1]->IntegerValue();
   int i;

   for (i = 0; i < sumCount; i++) {
       result = result + valueToSum;
   }

   args.GetReturnValue().Set(result);
}



void Initialize(Local<Object> exports) {
   NODE_SET_METHOD(exports, "helloWorld", HelloWorld);
   NODE_SET_METHOD(exports, "addNumbers", AddNumbers);
}

NODE_MODULE(addon, Initialize);

# 4. build addon /plugin for js
` node-gyp configure build `

# 5. how to use c++ in js

const addon = require('./build/Release/addon');

const addNumbersAddon = () => addon.addNumbers(3.14, 1000000);

const runAddon = () => addon.helloWorld();

const addNumbers = (number, addingTimes) => {
 console.time('C++');
 console.log(addNumbersAddon(number, addingTimes));
 console.timeEnd('C++');

 console.log('>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>');

 console.time('JS');
 console.log(addNumbersNode(number, addingTimes));
 console.timeEnd('JS');
};

addNumbers(3.14, 100000000);

# 6. run js
` node my.js`

