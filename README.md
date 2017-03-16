# testMyThread

これは与えられた関数を勝手にwhileループの中に押し込み，非同期に実行するクラスである．
使い方は，std::threadと同様にしてあります．
while()ループを多用するプログラマーは楽になるかも．．．

このサンプルでは以下のことをやっています．

1. 非同期で実行したい関数とその引数をコンストラクタに与える．
2. その関数をメンバ関数funcに格納する．
3. while()のなかでメンバ関数fancを実行する関数をつくる．
4. std::threadによって非同期実行

```c++
	#include <iostream>
	#include <functional>
	#include <thread>
	#include <string>
	
	class myThread
	{
	public:
		//与えられた関数を格納する関数ポインタ
		std::function<void()> func;
	
		//コンストラクタ．引数として，関数と任意長の引数を得る
	    template <class F, class... Args>
	    myThread(F&& f, Args&&... args)
	    {
	        //std::function<typename std::result_of<callable(arguments...)>::type()> task(std::bind(std::forward<callable>(f), std::forward<arguments>(args)...));
			this->func = std::bind(std::forward<F>(f), std::forward<Args>(args)...);
	    }
	
		//引数で与えられた関数を元にループを作る
		void makeLoop()
		{
			while(true){
				this->func();
			}
		}
	
		//スレッドにて非同期実行を行う
		void run()
		{
			std::thread th(&myThread::makeLoop, this);
			th.join();
		}
	
	};
	
	//テスト用関数.引数を出力するだけ
	void test(std::string arg)
	{
		std::cout << arg << std::endl;
	    return;
	}
	
	int main()
	{
		myThread thread1(test, "Thanks for your help!!");
		thread1.run();
	
	    return 0;
	}
```
