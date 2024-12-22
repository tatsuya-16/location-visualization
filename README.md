# location-visualization

## 概要
MQTT over WebSocketを使用して受信した位置情報データを，地図上にマーカーとしてリアルタイムに表示する．

## 必要なライブラリと依存関係
1. **Leaflet** (地図表示用)
   - CSS: https://unpkg.com/leaflet@1.5.1/dist/leaflet.css
   - JS: https://unpkg.com/leaflet@1.5.1/dist/leaflet.js
2. **Paho MQTT** (MQTT通信用)
   - JS: mqttws31.js
3. **jQuery** (DOM操作用)
   - JS: https://code.jquery.com/jquery-3.4.1.min.js

---

## 必要な設定
1. **MQTTブローカのアドレス，ポート，パス，およびトピック名**
     ```javascript
     const wsbroker = "";  // MQTTブローカのアドレス
     const wsport = 0;     // ポート番号
     const path = "";      // パス
     const topic = "";     // トピック名
     ```
2. **認証情報設定 (必要な場合のみ)**
   ```javascript
   const options = {
     useSSL: true,
     userName: "",  // ユーザー名
     password: "",  // パスワード
     timeout: 3,
   };
   ```
---

## 機能詳細
1. **地図の表示**
   - Leafletを使用してOpenStreetMapの地図を描画．
   - 初期表示位置は`35.1356448, 136.9760683`で，ズームレベルは16．
   ```javascript
   var mymap = L.map("mapid").setView([35.1356448, 136.9760683], 16);
   var tileLayer = L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
     attribution: '© OpenStreetMap contributors',
     maxZoom: 19,
   });
   tileLayer.addTo(mymap);
   ```

2. **MQTT通信の接続とメッセージ受信**
   - MQTTブローカへ接続し，指定されたトピックをサブスクライブ．
   - メッセージを受信すると，緯度と経度を取得し，地図上にマーカーを配置．
   ```javascript
   client.onMessageArrived = function (message) {
     var received_data = JSON.parse(message.payloadString);
     var lat = parseFloat(received_data.LATITUDE);
     var lon = parseFloat(received_data.LONGITUDE);
     L.marker([lat, lon]).addTo(mymap).bindPopup(truck_identifier);
   };
   ```

3. **ログと受信メッセージの表示**
   - 受信したメッセージと接続状態を表示．
   ```javascript
   function print_log(message) {
     var div = $("#log div");
     div.append($("<code>").text(message));
   }
   ```

---

## 設定とカスタマイズ
### 初期表示位置の変更
```javascript
mymap.setView([緯度, 経度], ズームレベル);
```

### マーカーのカスタマイズ
```javascript
L.marker([lat, lon], {icon: customIcon}).addTo(mymap);
```
