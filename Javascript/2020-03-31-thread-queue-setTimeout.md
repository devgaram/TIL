# 자바스크립트 동작 방식 - 스레드 큐와 setTimeout()

Date: Mar 31, 2020

- Title : 자바스크립트 동작 방식 - 스레드 큐와 setTimeout()
- Date : 2020-03-31
- Category : Javascript

# WHY?

자바스크립트가 단일 스레드 환경에서 돌아간다는 것은 대부분의 사람들이 알고 있을 것이다. 하지만 어떻게 동작하는 지는 모르는 사람이 있다.. ~~바로..나처럼..?~~ 그런 이유로 자바스크립트가 어떻게 돌아가는 지 낱낱이 파헤쳐 봐야지!

# 스레드 큐(태스크 큐)

스레드 큐는 단일 스레드 환경에서 어떠한 순서로 함수나 기능을 수행할지 결정하는 목록으로 FIFO(First-In First-Out)의 정책을 가진다. 스레드 큐 덕분에 지연이 발생하더라도 들어온 순서대로 요청을 처리할 수 있다. 

> ✨ 여기서 말하는 지연이란? 자바스크립트는 사용자와 상호작용하는 이벤트 기반 언어라, 비동기로 동작하거나 현재 소스가 실행 중에 사용자의 입력이 들어오면 지연이 발생할 수 있다.

# setTimeOut() 파헤치기

🎉아래 코드에서 `buttonAddItem.onclick`, `divItems.appendChild()`, `inputCloneItem.focus()` 가 스레드 큐에 어떤 순서로 들어갈지 생각해보자.

    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Document</title>
      </head>
      <body>
        <button id="addItem">Add New Input</button>
        <div id="items"></div>
        <script>
          (function() {
            const divItems = document.getElementById("items"),
              buttonAddItem = document.getElementById("addItem"),
              inputItem = document.createElement("input");
            buttonAddItem.onclick = () => {
              const inputCloneItem = inputItem.cloneNode(true);
              inputCloneItem.value = "Give me focus";
    
              divItems.appendChild(inputCloneItem);
              setTimeout(() => {
                inputCloneItem.focus();
              }, 0);
            };
          }());
        </script>
      </body>
    </html>

`setTimeout()` 함수의 두번째 파라미타를 0으로 했으니깐 버튼을 클릭하면 `divItems.appendChild(inputCloneItem)` 보다 `inputCloneItem.focus()` 가 먼저 들어가있을거라고 생각했다면...? 땡땡!!😂😂 정답은 아래와 같다. `setTimeout()` 함수를 0초 후에 호출되도록 설정하면 호출하자마자 실행되는 것이 아니라 UI 업데이트가 이루어지고 나서 호출된다. 즉, setTimeout() 함수는 특정 시간이 되면 콜백 함수를 호출하도록 실행 큐 뒤에 해당 함수를 넣는 거지 함수 자체를 호출한다는 의미가 아니다.

![https://raw.githubusercontent.com/devgaram/TIL/master/Javascript/images/2020-03-31-img/Untitled.png](https://raw.githubusercontent.com/devgaram/TIL/master/Javascript/images/2020-03-31-img/Untitled.png)

🎉 다음 예제를 통해 `setTimeout()` 함수와 단일 스레드 환경을 명확하게 이해해보자. 방금 예제와 비슷하지만 `setTimeout()`은 버튼이 클릭되고 2초 뒤에 콜백함수로 호출하도록 했고 `sleep()` 함수를 추가해서 10초동안 while 문이 돌아가게 했다. 자, 그럼 `setTimeout()`의 콜백함수는 언제 실행될까?

    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Document</title>
      </head>
      <body>
        <button id="addItem">Add New Input</button>
        <div id="items"></div>
        <script>
          (function() {
            const divItems = document.getElementById("items"),
              buttonAddItem = document.getElementById("addItem"),
              inputItem = document.createElement("input"),
              divLog = document.getElementById("log");
            buttonAddItem.onclick = () => {
              const inputCloneItem = inputItem.cloneNode(true),
                    waitSeconds = 10;
              
              inputCloneItem.value = "Give me focus";
    
              divItems.appendChild(inputCloneItem);
    
              console.log("Execute setTimeout at: \t" + Date.now());
              setTimeout(() => {
                inputCloneItem.focus();
                console.log("Execute setTimeout at: \t" + Date.now());
              }, 2000);
    
              sleep(waitSeconds * 1000);
            };
    
            function sleep(waitSeconds) {
              const waitUntil = Date.now() + waitSeconds;
              while (Date.now() < waitUntil);
            }
          }());
        </script>
      </body>
    </html>

아래의 실행결과를 보면 setTimeout() 안의 콜백함수는 2초 뒤에 실행되지않고 10초 뒤에 실행된다.

![https://raw.githubusercontent.com/devgaram/TIL/master/Javascript/images/2020-03-31-img/Untitled%201.png](https://raw.githubusercontent.com/devgaram/TIL/master/Javascript/images/2020-03-31-img/Untitled%201.png)

setTimeout()의 콜백함수는 버튼 클릭 2초 후 스레드 큐에 등록되지만 버튼 클릭 핸들러 내 sleep() 함수가 다 실행되어야 순서대로 처리되기 때문에 10초보다 뒤에 실행된다.

![https://raw.githubusercontent.com/devgaram/TIL/master/Javascript/images/2020-03-31-img/Untitled%202.png](https://raw.githubusercontent.com/devgaram/TIL/master/Javascript/images/2020-03-31-img/Untitled%202.png)

🎉 위 예제에서 버튼 클릭 시 setTimeout() 함수를 두번 호출하도록 코드를 수정해보고 실행이 어떤식으로 되는 지 알아보자.

    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Document</title>
      </head>
      <body>
        <button id="addItem">Add New Input</button>
        <div id="items"></div>
        <script>
          (function() {
            const divItems = document.getElementById("items"),
              buttonAddItem = document.getElementById("addItem"),
              inputItem = document.createElement("input"),
              divLog = document.getElementById("log");
            buttonAddItem.onclick = () => {
              const inputCloneItem = inputItem.cloneNode(true),
                    waitSeconds = 10;
              
              inputCloneItem.value = "Give me focus";
    
              divItems.appendChild(inputCloneItem);
    
              console.log("onclick function invoked at: \t" + Date.now());
              setTimeout(() => {
                console.log("1st Execute setTimeout at: \t" + Date.now());
                sleep(waitSeconds * 1000);
              }, 2000);
              setTimeout(() => {
                inputCloneItem.focus();
                console.log("2 Execute setTimeout at: \t" + Date.now());
              }, 2000);
    
              sleep(waitSeconds * 1000);
            };
    
            function sleep(waitSeconds) {
              const waitUntil = Date.now() + waitSeconds;
              while (Date.now() < waitUntil);
            }
          }());
        </script>
      </body>
    </html>

클릭 이벤트가 발생하면 setTimeout() 함수의 콜백 이벤트를 등록하고 2초 뒤에는 등록한 2개의 setTimeout() 함수가 호출된다(스레드 큐에 콜백함수가 들어간다) 이때 스레드 큐에서는 10초간 sleep()을 수행하기때문이 sleep()이 끝나고 UI 업데이트가 이루어진 후 첫번째 콜백이 실행된다. 콜백 안에 또 sleep()이 있어서 10초 뒤에 두번째 콜백이 수행된다. 즉, 두번째 setTimeout 콜백은 20초 뒤에나 실행된다.

![https://raw.githubusercontent.com/devgaram/TIL/master/Javascript/images/2020-03-31-img/Untitled%203.png](https://raw.githubusercontent.com/devgaram/TIL/master/Javascript/images/2020-03-31-img/Untitled%203.png)

스레드 큐는 아래와 같다.

![https://raw.githubusercontent.com/devgaram/TIL/master/Javascript/images/2020-03-31-img/Untitled%204.png](https://raw.githubusercontent.com/devgaram/TIL/master/Javascript/images/2020-03-31-img/Untitled%204.png)

# 결론

setTimeout() 뿐만 아니라 모든 XMLHttpRequest나 다른 콜백 이벤트가 발생했다고 해서 바로바로 현재의 스레드에 인터럽트를 걸고 실행하는 것이 아니라, **현재 실행하고 있는 스레드 큐가 끝나고 자신의 차리가 와야 실행된다**. 즉, 처리가 오래 걸리는 자바스크립트 함수가 있을 때는 호출이 이루어져야 하는 시간이 되어도 그 함수의 처리가 끝날 때까지 스레드 큐에서 대기한다.