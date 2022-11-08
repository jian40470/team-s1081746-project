# team-s1081746-project
clock with rtc
使用RTC做為時間並計算出時鐘的區間
實時時鐘是指可以像時鐘一樣輸出實際時間的電子設備，
一般會是積體電路，因此也稱為時鐘晶片。
此名詞常用來表示在個人電腦、伺服器或嵌入式系統中有此機能的設備，
不過許多需要精確時的系統都會有此功能。
 實時時鐘和定時器訊號不同，
後者只是數位電路中一個表示時間的方波訊號，
而且不會以日常使用的時間單位表示。 
，DS3231 (+AT24C32, 32KBytes EEPROM)
 RTC 模組會在程式編譯上傳的同時被更新年份、
日期和時間，配合整合型 LCD (I2C模式) 
和大型數字顯示方式，
分別在 LCD 上以四個不同頁面分別以年、日/月、
(12/24格式)小時:分鐘和完整格式的方式顯示，
藉由這種方式讓使用者了解 RTC 模組的基本使用方法
接線完成之後，先來認識等一下會用到的 DS3231 暫存器。基本上
，只有位址 0x00 ~ 0x06 會用到，分別代表秒、分鐘、小時、日、月和年，
可讀取或是寫入 (變更) 時間，是 BCD 碼表示法
void setup () {

  Serial.begin(9600);

  delay(3000); // wait for console opening

  if (! rtc.begin()) {
    Serial.println("Couldn't find RTC");
    while (1);
  }

  if (rtc.lostPower()) {
    Serial.println("RTC lost power, lets set the time!");
    // following line sets the RTC to the date & time this sketch was compiled
    DateTime timeupdate = DateTime( F( __DATE__ ), F( __TIME__ ) ) + TimeSpan( /*days*/0, /*hours*/0, /*minutes*/0, /*seconds*/20 );
    // This line sets the RTC with an explicit date & time, for example to set
    // January 21, 2014 at 3am you would call:
    rtc.adjust( timeupdate );
  }
}timeupdate 是最終要寫入到 RTC 模組裡面 (暫存器位址 0x00 ~ 0x06 ) 設定時間的，包含下面兩個部分的加總：
DateTime( F( __DATE__ ), F( __TIME__ ) ) 
這一部分所產生出來的時間是程式編譯的時間 (2017/08/10 23:04:10)
TimeSpan( /*days*/0, /*hours*/0, /*minutes*/0, /*seconds*/20 )
這一部分我添加上去，用來補償編譯至上傳完成之後執行 RTC 模組時間更新的時間差。如果沒有加上這一段的話，
則最後在 Serial Monitor 上面的時間會與作業系統的時間出現多十幾秒以上的時間差。
TimeSpan 的設定應該只需要設定 "senonds"，其他欄位只要設定為 0 即可。那麼要設定為多少呢 ? 
這取決於電腦速度會有不同的編譯時間，不過使用 Arduino IDE 編譯時並不會幫我們計算編譯至完成程式上傳的時間，所以必須自己算；在按下編譯按鈕的同時開始計時，完成編譯停止計時，然後將這時間填入到 TimeSpan 的欄位中進行第一次更新。
