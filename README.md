# Selenium_chrome_driver_Naver_Captcha_Incapacitation
Selenium chrome driver 네이버 캡차(Captcha) 무력화

#히라마 블로그 ( 원본 ) 
https://hyrama.com/?p=693


네이버가 작년 9월(?)부터 사용자 입력을 제외한 소프트웨어적 입력을 통한 로그인을 막기 위해 Captcha 시스템을 도입했습니다. 이로 인해 Selenium을 이용하여 자동 로그인을 시도했던 사용자들이  Captcha에 탐지 당해 로그인을 하지 못하고 있습니다.

Selenium Element send_keys Method


Selenium을 이용하고 있는 대부분의 이용자들은 element.send_keys() 메서드를 이용하여 로그인을 시도했습니다. 그러나 Selenium의 driver를 이용하여 아이디 및 비밀번호를 element.send_keys()메서드를 이용하여 로그인 시도 시 비정상적인 접근 방식으로 네이버 Captcha가 탐지하게 됐습니다.

Selenium Element send_keys Method Part 2


비밀번호를 제외한 아이디만 element.send_keys()메서드를 통해 입력한 경우에도 네이버 Captcha가 탐지합니다.

결론적으로 말씀을 드리자면 element를 통한 send_keys()는 네이버 Captcha에 반드시 탐지가 됩니다. 그렇다면 Selenium element send_keys() 메서드를 제외한 Win32api를 이용한 로그인 시도는 어떻게 될지 궁금해 시도해 봤습니다.

Win32api


Win32api를 통해 입력한 경우 Captcha가 탐지하지 못하고 로그인이 정상적으로 진행 됐습니다. 그러나 Win32api를 이용할 때는 driver에 –headless 옵션을 추가 할 경우 focus를 못잡아 아이디 및 비밀번호가 전달이 안 돼 로그인 시도가 거의 불가능합니다. 즉 사용자가 focus를 네이버 로그인 창이 아닌 다른 곳에 focus하는 경우 로그인에 문제가 생깁니다.

Win32api를 통해 성공할 수는 있으나 focus가 안 된 상태에서는 불가능한 제약이 생겼습니다. 하지만 driver가 –headless 상태에서 focus도 안 주고 element.send_keys()메서드를 통하여 Captcha를 피해 로그인을 성공시킬 수 있는 방법이 하나 있습니다.

Clipboard with send_keys Method


그것은 바로 Key 조합을 보내는 것입니다. Ctrl+V 키 조합을 element에 보낼 경우 네이버 Captcha가 비정상적인 로그인 시도를 탐지하지 못하고 정상적인 로그인에 성공합니다.
Clipboard 기능을 이용하여 아이디 및 비밀번호를 복사한 후에 로그인창에 붙여넣기를 하면 쉽게 네이버 Captcha를 뚫을 수 있습니다. element.send_keys()로 보내는 것이기 때문에 driver가 –headless인 상태에서도 사용이 가능합니다.

1
2
3
4
5
6
7
8
 
def clipboard_input(self, user_xpath, user_input):
        pyperclip.copy(user_input) # input을 클립보드로 복사
 
        self.driver.find_element_by_xpath(user_xpath).click() # element focus 설정
        ActionChains(self.driver).key_down(Keys.CONTROL).send_keys('v').key_up(Keys.CONTROL).perform() # Ctrl+V 전달
        time.sleep(1)
 
네이버 관련 시스템을 자동화하여 이용하기 위해 네이버 로그인을 시도했는데 Captcha라는 시스템이 작년 말부터 추가가 돼 소프트웨어적으로 로그인 시도가 어려웠으나 다양한 방법을 시도한 끝에 Clipboard를 이용한 로그인 방법을 찾게 됐습니다. 뿐만 아니라 Win32api도 가능하며 현재 Win32api로 –headless인 상태에서는 로그인 가능한 방법은 개발하지 못했으나 Unactive Window를 이용하면 driver가 –headless인 상태에서도 Captcha를 무력화할 수 있을 것 같습니다.

모든 소스는 https://github.com/lumyjuwon/NaverCaptcha에서 확인하실 수 있습니다.
