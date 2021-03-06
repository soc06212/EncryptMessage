# 구현 내용
1. UI 및 디자인

   HTML 코드로 레이아웃을 제작하였고, CSS로 디자인하였습니다. JavaScript의 prompt() 함수로 간단히 사용자의 입력 값을 받아올 수 있지만, 사용자 경험적인 측면에서 볼 때 사용자가 사용할 수 있는 옵션이 적어진다는 점, 디자인적으로 활용하기 힘들다는 점 등을 고려하여 정적 HTML 코드로 UI를 제작하기로 했습니다. 다음은 사용자로부터 값을 입력 받기 위한 textarea 태그에서 입력 값을 받아오는 코드 예시입니다.

   **HTML**
   ```html
   <strong id="label_input" style="color: cadetblue">입력</strong><br>
   <textarea type="text" id="input_message" placeholder="암호화할 메시지를 입력하세요" cols="50" rows="10"></textarea>
   ```

   **JavaScript**
   ```JavaScript
   var txtInput = document.getElementById("input_message");
   var inputValue = txtInput.value;
   ```

   또한 UI 디자인적으로 일관성을 주기 위해 사용자에게 알림을 표시하는 경우에도 JavaScript의 alert() 함수 대신 자체 알림창을 만들어 표시하도록 했습니다.

   **Screenshot**
   
   <img src="/screenshots/screenshot_05.png" alt="Alert dialog screenshot" width="250">

   **HTML**
   ```html
   <!--알림창-->
    <div id="div_popup_bg" hidden="true"></div>
    <div id="div_popup" hidden="true">
        <h2 id="popup_title">Title</h2>
        <p id="popup_message" style="color: dimgray">This is an example of popup message. Message's going to show up here.<br>This shows what if content is too long.</p>
        <button id="popup_close">닫기</button>
    </div>
   ```

   **JavaScript**
   ```JavaScript
   function showPopupMessage(title, message) {
      popupBg.hidden = false;
      popupLayout.hidden = false;
      popupLayout.width = (document.width / 5) * 4;
      popupTitle.textContent = title;
      popupMessage.textContent = message;
   }
   
   ...
   
   showPopupMessage("빈 항목", "암호화 할 메시지를 입력해 주세요.");
   ```

   평소에는 hidden=”true” 옵션으로 숨겨져 있다가 showPopupMessage() 함수를 호출하면 값이 false가
   되어 표시됩니다.

2. 암호화

   ```JavaScript
   function encrypt() {
      var message = startTag + txtInput.value + endTag;
      var rawCode = "";
      var password = txtPassword.value;
      var converted = "";
      var indexOfPw = 0;
      for (var i = 0; i < message.length; i++) {
           if (indexOfPw >= password.length) {
               indexOfPw = 0;
           }
           var encryptedUnicode = (message[i].charCodeAt(0) + password[indexOfPw].charCodeAt(0));
           rawCode += encryptedUnicode + " ";
           converted += String.fromCharCode(encryptedUnicode);
           indexOfPw++;
      }
      logs.push("rawCode: " + rawCode);
      return converted;
   }
   ```
   암호화를 진행하는 함수는 위와 같습니다. 우선 입력한 메시지와 비밀번호를 받아와 각각 message와 password 변수에 저장합니다. 그 다음 가져온 데이터를 JavaScript의 charCodeAt() 함수를 사용해 유니코드로 변환합니다. 암호화는 이 비밀번호의 각 유니코드와 메시지의 각 유니코드를 더한 값을 다시 String.fromCharCode()를 사용해 변환하여 진행합니다.

3. 복호화
   
   ```JavaScript
   function decrypt(printLog) {
      var message = txtInput.value;
      var password = txtPassword.value;
      var rawCode = "";
      var converted = "";
      var indexOfPw = 0;
      for (var i = 0; i < message.length; i++) {
         if (indexOfPw >= password.length) {
               indexOfPw = 0;
         }
         var encryptedUnicode = (message[i].charCodeAt(0) - password[indexOfPw].charCodeAt(0));
         rawCode += encryptedUnicode + " ";
         converted += String.fromCharCode(encryptedUnicode);
         indexOfPw++;
      }
      if (printLog) {
         logs.push("rawCode: " + rawCode);
      }
      return converted;
   }
   ```
   복호화를 진행하는 함수는 위와 같습니다. 우선 입력한 문자열과 비밀번호를 받아와 각각 message와 password 변수에 저장합니다. 그 다음 가져온 데이터를 JavaScript의 charCodeAt() 함수를 사용해 유니코드로 변환합니다. 암호화는 이 각 문자열의 유니코드에서 각 유니코드와 비밀번호의 각 유니코드를 뺀 값을 다시 String.fromCharCode()를 사용해 변환하여 진행합니다.

4. ‘암호화/ 복호화’ 버튼 클릭 시

   ‘암호화/ 복호화’ 버튼을 클릭하면 암호화 혹은 복호화를 진행하도록 이벤트 리스너를 등록합니다.

   ```JavaScript
    btnConvert.addEventListener("click", function () { ... });
   ```

   사용자가 ‘암호화/ 복호화’ 버튼을 클릭하면 프로그램은 필요한 정보들이 모두 유효한지 확인하는 작업을 진행하는 checkValid() 함수를 호출합니다.

    ```javascript
    function checkValid() {
        if (txtInput.value == "") {
            showPopupMessage("빈 항목", "암호화 할 메시지를 입력해 주세요.");
            return false;
        } else {
            if (txtPassword.value == "") {
                var confirmWindow = confirm("비밀번호를 입력하지 않았습니다.\n\n임의의 비밀번호를 생성할까요?");
                if (confirmWindow) {
                    txtPassword.value = buildPW();
                    logs.push("password generated: true");
                } else {
                    return false;
                }
            }
        }
        return true;
    }
    ```

   checkValid() 함수는 우선 암호화 할 메시지가 입력되었는지 확인합니다. 메시지가 입력되지 않았다면 사용자에게 오류 메시지를 띄우고 작업을 종료합니다. 메시지가 입력되었다면 다음으로 암호화에 사용할 비밀번호가 입력되었는지 확인합니다. 비밀번호가 입력되었다면 예정대로 다음 작업을 시작하고, 입력되지 않았다면 사용자에게 비밀번호를 자동으로 생성할지 여부를 묻습니다. 사용자의 응답에 따라 자동으로 비밀번호를 생성한 후 계속하거나 작업을 종료합니다. 만약 비밀번호를 자동으로 생성하겠다고 응답할 경우 buildPW() 함수를 호출하여 비밀번호를 생성합니다.

   
    ```JavaScript
    function buildPW() {
        var pw = "";
        for (var i = 0; i < 12; i++) {
            var ranNum = Math.floor(Math.random() * 'z'.charCodeAt(0));
            var word = String.fromCharCode(ranNum);
            pw += word;
        }
        return pw;
    }
    ```

   buildPW() 함수는 JavaScript의 Math.random() 함수에 기반하여 최대 12 길이의 랜덤한 비밀번호를 생성합니다. 생성된 비밀번호는 반환되어 비밀번호 창에 입력됩니다.

   checkValid() 가 true로 판명이 나면 다음으로 현재 사용자가 수행중인 작업이 암호화인지, 복호화인지를 판별합니다.

   

   수행중인 작업을 암호화와 복호화로 구분하는 기준은 다음과 같습니다.

   한번 암호화를 진행한 메시지일 경우, 복호화를 진행했을 때 다음 태그가 앞 뒤에 붙어있습니다.

    ```JavaScript
    const startTag = "<msg>";
    const endTag = "</end>";
    ```

   만약 “This is a sample message”이라는 문자열을 암호화 한다면, 우선 앞 뒤에 태그를 붙입니다. 따라서  “<msg>This is a sample message</end>”라는 문자열이 생성됩니다. 이후 이 문자열을 사용자가 입력한 비밀번호를 기반으로 암호화를 진행합니다. 그렇기 때문에 같은 비밀번호를 사용하여 복호화를 진행하면 위의 태그가 메시지에 남게 되는 것입니다.

    ```JavaScript
    function checkConvertMode() {
        var converted = decrypt(false);
        if (converted.startsWith(startTag) || converted.endsWith(endTag)) {
            logs.push("mode: decrypt");
            if (converted.startsWith(startTag) && converted.endsWith(endTag)) {
                logs.push("distorted: false");
                return 0;
            } else {
                logs.push("distorted: true");
                return 2;
            }
        } else {
            logs.push("mode: encrypt");
            logs.push("distorted: false");
            return 1;
        }
    }
    ```

   checkConvertMode() 함수는 이와 같은 방법으로 사용자가 진행하는 작업을 검사하여 복호화일 경우 0을, 암호화일 경우 1을 반환합니다. 

   암호 메시지는 복호화할 데이터가 손상되었는지 여부도 검사하는데, 앞서 설명한 메시지 태그들이 온전치 못하게 복호화 되었을 경우 이를 데이터 손상으로 간주합니다. 모종의 이유로 암호화된 메시지의 앞이나 뒤가 잘려서 전송되었을 경우가 이에 해당합니다. 이 경우 복호화를 진행하는 사용자는 메시지를 전송한 사람으로부터 새로운 메시지를 요청해야 할 수 있습니다.

    ```JavaScript
    case 0: // 복호화 모드
                    var decryptedMsg = decrypt(true);
                    var outputMsg = decryptedMsg.substring(
                        (decryptedMsg.indexOf(startTag) +
                            startTag.length), decryptedMsg.indexOf(endTag));

                    logs.push("password: " + txtPassword.value);
                    txtOutput.value = outputMsg;
                    break;
    ```

   복호화 모드일 경우 예정대로 복호화를 진행합니다. 이후 결과 메시지를 결과 창에 출력합니다.

    ```JavaScript
    case 1: // 암호화 모드
                    if (cbStrictMode.checked) {
                        if (checkStrictPassword()) {
                            var encryptedMsg = encrypt();
                            logs.push("password: " + txtPassword.value);

                            txtOutput.value = encryptedMsg;
                        } else {
                            logs.push("encrypt failed: password error\nerror message \"엄격모드에서 비밀번호는 8자리 이상이며,  영문자(소문자, 대문자), 숫자, 특수부호를 모두 포함해야 합니다.\"");
                        }
                    } else {
                        var encryptedMsg = encrypt();
                        logs.push("password: " + txtPassword.value);
                        txtOutput.value = encryptedMsg;
                    }
                    break;
    ```

   암호화 모드일 경우에는 사용자가 엄격 모드를 사용할 때의 작업이 추가됩니다. 엄격 모드는 사용자가 예측하기 힘든 비밀번호를 사용하도록 유도합니다. 

    ```JavaScript
    function checkStrictPassword() {
        var validate = {
            small: false,
            capital: false,
            number: false,
            symbol: false
        }

        var password = txtPassword.value;
        if (password.length < 8) {
            showPopupMessage("엄격 모드", "8자 이상의 비밀번호를 입력하십시오.");
            return false;
        }

        for (var i = 0; i < password.length; i++) {
            var p = password[i];
            if (p >= 'A' && p <= 'Z') {
                validate.capital = true;
            } else if (p >= 'a' && p <= 'z') {
                validate.small = true;
            } else if ((p >= '!' && p <= '/') ||
                (p >= '{' && p <= '~')) {
                validate.symbol = true;
            } else if (p >= '0' && p <= '9') {
                validate.number = true;
            }
        }
        if (validate.small) {
            logs.push("password includes: small letters");
            if (validate.capital) {
                logs.push("password includes: capital letters");
                if (validate.symbol) {
                    logs.push("password includes: symbols");
                    if (validate.number) {
                        logs.push("password includes: numbers");
                        return true;
                    }
                }
            }
        }
        showPopupMessage("엄격 모드", "엄격모드에서 비밀번호는 영문자(소문자, 대문자), 숫자, 특수부호를 모두 포함해야 합니다.");
        return false;
    }
    ```

   엄격 모드에서 사용자는 8자리 이상의 비밀번호를 영문자(대문자와 소문자), 숫자, 특수문자를 모두 포함하여 만들어야 합니다. 이 조건을 만족하면 checkStrictPassword() 함수는 true를 반환합니다. 그렇지 않을 경우는 사용자에게 경고 메시지를 띄우고 false를 리턴합니다. 

   엄격 모드를 사용하는 경우 checkStrictPassword()로 비밀번호를 확인하고, 반환되는 값이 true일 경우 예정대로 암호화를 진행합니다. false일 경우 현재 진행중인 작업을 종료합니다.

   엄격 모드를 사용하지 않는 경우 예정대로 암호화를 진행하여 결과를 결과 창에 출력합니다.

    ```JavaScript
    case 2: // 복호화 중 데이터 손상을 감지한 경우
                    showPopupMessage("데이터 손상 감지", "데이터를 검사하던 중 데이터가 온전하지 않은 것을 발견했습니다. 보안을 위해 데이터를 복구하지 않았습니다. 올바른 데이터로 다시 시도해 주십시오.");
                    logs.push("password: " + txtPassword.value, "input:" + txtInput.value);
                    txtOutput.value = "데이터가 손상되어 복호화를 진행하지 않았습니다.\n\n결과 로그를 참조해 주십시오."
                    break;
    ```

   복호화 모드에서 데이터 손상을 감지한 경우는 사용자에게 경고 메시지를 출력하고 작업을 종료합니다. 결과 창에는 복호화를 진행하지 않았다는 메시지를 출력합니다.

5. 도움말

   암호 메시지는 사용자가 프로그램을 더 잘 이용할 수 있도록 도움말을 제공합니다.

    ```html
    <div id="div_help" style="width: 100%; position: absolute; top: 0; left: 0; background: #fff;" hidden="hidden"> ... </div>
    ```

   도움말은 HTML 코드로 이루어져 있습니다. 평소에는 hidden 속성으로 사용자에게 표시되지 않습니다.

    ```JavaScript
    btnHelp.addEventListener("click", function () {
        helpLayout.hidden = false;
    });
    ```

   ‘?’ 버튼을 클릭하면 도움말 레이아웃을 표시할 수 있도록 이벤트 리스너를 등록하였습니다. hidden 속성의 값을 false로 바꾸어 레이아웃을 표시합니다.

    ```JavaScript
    helpClose.addEventListener("click", function () {
        helpLayout.hidden = true;
    });
    ```

   ‘도움말 닫기’ 버튼을 클릭하면 도움말 레이아웃을 숨기도록 이벤트 리스너를 등록하였습니다. hidden 속성의 값을 바꾸어 레이아웃을 숨깁니다.

6. 결과 로그

   결과 로그는 textarea 태그를 통해 출력됩니다. 결과 로그도 마찬가지로 평소에는 hidden 속성으로 숨겨져 있습니다.

    ```html
    <p>
        <input type="checkbox" id="cb_show_log">
        <label for="cb_show_log">결과 로그 표시</label>
        <br><textarea type="text" id="output_log" cols="50" rows="5" readonly="true" hidden="true"></textarea>
    </p>
    ```

   ‘결과 로그 표시’ 체크박스를 체크하거나 해제하면 값이 변경되면서 다음 이벤트 리스너를 실행합니다.

    ```JavaScript
    cbShowLog.addEventListener("change", function () {
        if (this.checked) {
            txtLog.hidden = false;
        } else {
            txtLog.hidden = true;
        }
    });
    ```
