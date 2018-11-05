---
refcn: chapter_02/05_transport
refen: configuration/transport
---
# 전송 설정

전송은 V2Ray가 동료들과 데이터를주고받는 방법입니다. 전송의 책임은 데이터를 피어로 안정적으로 전송하는 것입니다. 일반적으로 연결에는 양쪽 끝점에서 일치하는 전송이 있습니다. 예를 들어 V2Ray 아웃 바운드가 전송으로 WebSocket을 사용하는 경우 인바운드는 WebSocket을 사용해야하며, 그렇지 않으면 연결을 설정할 수 없습니다.

전송 설정은 전역 설정과 프록시 설정의 두 부분으로 나뉩니다. 프록시 별 설정은 각 프록시가 데이터를 처리하는 방법을 지정하고 전역 설정은 모든 프록시를 지정합니다. 일반적으로 연결 피어 간의 인바운드 및 아웃 바운드 프록시는 동일한 전송 설정을 가져야합니다. 프록시에 전송 설정이 없으면 전역 설정이 적용됩니다.

## TransportObject

`TransportObject` 은 최상위 레벨 구성에서 `전송` 필드로 사용됩니다.

```javascript
{
  "tcpSettings": {},
  "kcpSettings": {},
  "wsSettings": {},
  "httpSettings": {},
  "dsSettings": {}
}
```

> `tcpSettings`: TcpObject

[TCP 전송 설정](transport/tcp.md).

> `kcpSettings`: KcpObject

[mKCP 전송 설정](transport/mkcp.md).

> `wsSettings`: WebSocketObject

[WebSocket 전송 설정](transport/websocket.md).

> `httpSettings`: HttpObject

[HTTP / 2 전송 설정](transport/h2.md).

> `dsSettings`: DomainSocketObject

[도메인 소켓 전송 설정](transport/domainsocket.md).

## StreamSettingsObject

각 인바운드 및 아웃 바운드 프록시는 최상위 레벨 구성의 `streamSettings` 필드에 지정된대로 자체 전송 설정을 가질 수 있습니다.

```javascript
{
  "네트워크": "TCP"
  "보안": "없음",
  "tlsSettings"{}
  "tcpSettings"{},
  "kcpSettings"{},
  "wsSettings"{} ,
  "httpSettings": {},
  "dsSettings": {},
  "sockopt": {
    "mark": 0,
    "tcpFastOpen": false,
    "tproxy": "off"
  }
}
```

> `네트워크`: "tcp"| "kcp"| "ws"| "http"| "domainsocket"

스트림 전송의 네트워크 유형. 기본값 `"tcp"`.

> `보안`: "없음"| "tls"

보안 유형. 선택은 없다 `"없음"` 별도의 보안 (기본값) 또는 `"TLS"` 사용하는 [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security).

> `tlsSettings`: [TLSObject](#tlsobject)

TLS 설정. TLS는 Golang에서 제공합니다. 최대 TLS 1.2를 지원합니다. DTLS는 지원되지 않습니다.

> `tcpSettings`: [TcpObject](transport/tcp.md)

현재 프록시에 대한 TCP 전송 구성입니다. 프록시가 TCP 전송을 사용할 때만 유효합니다. 구성은 전역 구성과 동일합니다.

> `kcpSettings`: KcpObject

현재 프록시에 대한 mKCP 전송 구성. 프록시가 mKCP 전송을 사용할 때만 유효합니다. 구성은 전역 구성과 동일합니다.

> `wsSettings`: WebSocketObject

현재 프록시에 대한 WebSocket 전송 구성입니다. 프록시가 WebSocket 전송을 사용할 때만 유효합니다. 구성은 전역 구성과 동일합니다.

> `httpSettings`: HttpObject

현재 프록시에 대한 HTTP / 2 전송 구성 프록시가 HTTP / 2 전송을 사용하는 경우에만 유효합니다. 구성은 전역 구성과 동일합니다.

> `dsSettings`: DomainSocketObject

현재 프록시에 대한 도메인 소켓 전송 구성입니다. 프록시가 도메인 소켓 전송을 사용하는 경우에만 유효합니다.

> `sockopt`: SockoptObject

들어오고 나가는 연결을위한 소켓 옵션.

### TLSObject

```javascript
{
  "serverName": "v2ray.com",
  "allowInsecure": false,
  "alpn": [ "http / 1.1"],
  "인증서": []
}
```

> `serverName`: 문자열

TLS 인증에 사용되는 서버 이름 (일반적으로 도메인)입니다. 일반적으로 이것은 상응하는 인바운드 / 아웃 바운드가 통신을 위해 IP를 사용할 때 사용됩니다.

> `alpn`: \ [string \]

TLS 핸드 셰이크의 ALPN 값을 지정하는 문자열 배열입니다. 기본값은 `[ "http / 1.1"]입니다.`.

> `allowInsecure`: true | 그릇된

`참`이면 V2Ray는 TLS 클라이언트에서 안전하지 않은 연결을 허용합니다. 예를 들어, TLS 서버는 확인할 수없는 인증서를 사용합니다.

> `allowInsecureCiphers`: true | 그릇된

안전하지 못한 암호 모음을 허용하거나 금지합니다. 기본적으로 TLS는 TLS 1.3 사양의 암호화 제품군 만 사용합니다. 이 옵션을 켜면 정적 RSA 키가있는 암호 그룹을 허용 할 수 있습니다.

> `인증서`: \ [ [CertificateObject](#certificateobject)\]

TLS 인증서 목록. 각 항목은 하나의 인증서입니다.

### CertificateObject

```javascript
{
  "usage": "encipherment",

  "certificateFile": "/path/to/certificate.crt",
  "keyFile": "/path/to/key.key",

  "certificate": [
    " ----- "----- 인증서 BEGIN
    "MIICwDCCAaigAwIBAgIRAO16JMdESAuHidFYJAR / 7kAwDQYJKoZIhvcNAQELBQAw "
    ", "ADAeFw0xODA0MTAxMzU1MTdaFw0xODA0MTAxNTU1MTdaMAAwggEiMA0GCSqGSIb3을
    "DQEBAQUAA4IBDwAwggEKAoIBAQCs2PX0fFSCjOemmdm9UbOvcLctF94Ox4BpSfJ + "
    ", 3lJHwZbvnOFuo56WhQJWrclKoImp / c9veL1J4Bbtam3sW3APkZVEK9UxRQ57HQuw "
    "OzhV0FD20 / 0YELou85TwnkTw5l9GVCXT02NG + pGlYsFrxesUHpojdl8tIcn113M5 "
    "을 pypgDPVmPeeORRf7nseMC6GhvXYM4txJPyenohwegl8DZ6OE5FkSVR5wFQtAhbON "
    "OAkIVVmw002K2J6pitPuJGOka9PxcCVWhko / W + + JCGapcC7O74palwBUuXE1iH JP "
    "noPjGp4qE2ognW3WH / sgQ + rvo20eXb9Um1steaYY8xlxgBsXAgMBAAGjNTAzMA4G "
    "A1UdDwEB / wQEAwIFoDATBgNVHSUEDDAKBggrBgEFBQcDATAMBgNVHRMBAf8EAjAA "
    "MA0GCSqGSIb3DQEBCwUAA4IBAQBUd9sGKYemzwPnxtw / vzkV8Q32NILEMlPVqeJU "
    "7UxVgIODBV6A1b3tOUoktuhmgSSaQxjhYbFAVTD LUglMUCxNbj56luBRlLLQWo + + ",
    "9BUhC / ow393tLmqKcB59qNcwbZER6XT5POYwcaKM75QVqhCJVHJNb1zSE E7Co7iO "
    "6wIan3lFyjBfYlBEz5vyRWQNIwKfdh5cK1yAu13xGENwmtlSTHiwbjBLXfk + 0A / 8 ",
    "R / 2S + + sCYUkGZHhj8xY7bJ1zg0FRalP5LrqY r6BckT1QPDIQKYy615j1LpOtwZe / "
    "d4q7MD / dkzRDsch7t2cIjM / PYeMuzh87admSyL6hdtK0Nm / Q ",
    "----- ----- END 인증서 "
  ,
  "키":
    "----- RSA 개인 키 BEGIN"
    "MIIEowIBAAKCAQEArNj19HxUgoznppnZvVGzr3C3LRfeDseAaUnyft5SR8GW75zh"
    "bqOeloUCVq3JSqCJqf3Pb3i9SeAW7Wpt7FtwD5GVRCvVMUUOex0LsDs4VdBQ9tP9"
    "GBC6LvOU8J5E8OZfRlQl09NjRvqRpWLBa8XrFB6aI3ZfLSHJ9ddzOacqYAz1Zj3n"
    "jkUX + 57HjAuhob12DOLcST8np6IcHoJfA2ejhORZElUecBULQIWzjTgJCFVZsNNN"
    "itieqYrT7iRjpGvT8XAlVoZKP1viQhmqXAuzu + KWpcAVLlxNYh / iaZ6D4xqeKhNq"
    "IJ1t1h / 7IEPq76NtHl2 / VJtbLXmmGPMZcYAbFwIDAQABAoIBAFCgG4phfGIxK9Uw"
    "QRP + o9xQLYGhQnmOYb27OpwnRCYojSlT + mvLcqwvevnHsr9WxyA + PkZ3AYS2PLue"
    "C4xW0pzQgdn8wENtPOX8lHkuBocw1rNsCwDwvIguIuliSjI8o3CAy + xVDFgNhWap"
    "/ CMzfQYziB7GlnrM6hH838iiy0dlv4I / HKK + 3 / YlSYQEvnFokTf7HxbDDmznkJTM"
    "aPKZ5qbnV + 4AcQfcLYJ8QE0ViJ8dVZ7RLwIf7 + SG0b0bqloti4 + oQXqGtiESUwE W ",
    "/ Wzi7oyCbFJoPsFWp1P5 + wD7jAGpAd9lPIwPahdr1wl6VwIx9W0XYjoZn71AEaw4 "
    "bK4xUXECgYEA3g2o9WqyrhYSax3pGEdvV2qN0VQhw7Xe + jyy98CELOO2DNbB9QNJ "
    "8cSSU / PjkxQlgbOJc8DEprdMldN5xI / srlsbQWCj72wXxXnVnh991bI2clwt7oYi "
    "pcGZwzCrJyFL + QaZmYzLxkxYl1tCiiuqLm + EkjxCWKTX / kKEFb6rtnMCgYEAx0WR "
    "L8Uue3lXxhXRdBS5QRTBNklkSxtU + 2yyXRpvFa7Qam + GghJs5RKfJ9lTvjfM / PxG "
    "3vhuBliWQOKQbm1ZGLbgGBM505EOP7DikUmH / kzKxIeRo4l64mioKdDwK / 4CZtS7"
    "az0Lq3eS6bq11qL4mEdE6Gn / Y + sqB83GHZYju80CgYABFm4KbbBcW + 1RKv9WSBtK"
    "gVIagV / 89moWLa / uuLmtApyEqZSfn5mAHqdc0 + f8c2 / Pl9KHh50u99zfKv8AsHfH"
    "TtjuVAvZg10GcZdTQ / I41ruficYL0gpfZ3haVWWxNl + J47di4iapXPxeGWtVA + U8"
    "eH1cvgDRMFWCgE7nUFzE8wKBgGndUomfZtdgGrp4ouLZk6W4ogD2MpsYNSixkXyW"
    " 64cIbV7uSvZVVZbJMtaXxb6bpIKOgBQ6xTEH5SMpenPAEgJoPVts816rhHdfwK5Q "
    "8zetklegckYAZtFbqmM0xjOI6bu5rqwFLWr1xo33jF0wDYPQ8RHMJkruB1FIB8V2 "
    "GxvNAoGBAM4g2z8NTPMqX + 8IBGkGgqmcYuRQxd3cs7LOSEjF9hPy1it2ZFe / yUKq "
    "ePa2E8osffK5LBkFzhyQb0WrGC9ijM9E6rv10gyuNjlwXdFJcdqVamxwPUBtxRJR "
    "cYTY2HRkJXDdtT0Bkc3josE6UUDvwMpO0CfAETQPto1tjNEDhQhT",
    "----- END RSA 개인 키 -----"
  ]
}
```

> `사용`: "암호화"| "확인"| "발행물"

인증서의 목적. 기본값 `"암호화"`. 선택 사항은 다음과 같습니다.

* `"암호화"`: 인증서는 TLS 인증 및 암호화에 사용됩니다.
* `"verify"`: 인증서는 원격 피어에서 TLS 인증서의 유효성을 검사하는 데 사용됩니다. 이 경우 인증서는 CA 인증서 여야합니다.
* `"issue"`: 인증서는 다른 인증서를 발행하는 데 사용됩니다. 이 경우 인증서는 CA 인증서 여야합니다.

{% hint style = 'info'%} Windows에서 CA로부터 발행 된 인증서를 확인하려면 시스템에 CA 인증서를 설치해야합니다. {% endhint %}

{% 힌트 스타일 = '정보'%} 새로운 클라이언트 요청이있을 때, 말할 `SERVERNAME` = `"v2ray.com"`, V2Ray가에 대한 인증서 발견 할 것이다 `"v2ray.com을"` 첫째. 찾을 수없는 경우, V2Ray 누구의 기존 인증서를 사용하여 새 인증서를 발급하려고합니다 `사용` 인 `"문제"` 에 대한 `"v2ray.com"`. 새 인증서는 한 시간 내에 만료되며 나중에 다시 사용할 수 있도록 인증서 풀에 추가됩니다. {% endhint %}

> `인증서 파일`: 문자열

인증서의 파일 경로입니다. 인증서가 OpenSSL에 의해 생성되면 경로는 ".crt"로 끝납니다.

{% hint style = 'info'%} `v2ctl cert -ca` 명령을 사용하여 새로운 CA 인증서를 생성하십시오. {% endhint %}

> `인증서`: \ [string \]

인증서의 내용으로 문자열 목록. 위의 예를 참조하십시오. `인증서` 또는 `인증서 파일` 은 비워 둘 수 없습니다.

> `keyFile`: 문자열

개인 키의 파일 경로입니다. OpenSSL에 의해 생성 된 파일은 일반적으로 ".key"로 끝납니다. 암호가있는 키 파일은 지원되지 않습니다.

> `키`: \ [string \]

개인 키의 내용으로 문자열 목록. 위의 예를 참조하십시오. `키` 또는 `keyFile` 은 비워 둘 수 없습니다.

`certificateFile` 및 `certificate` 이 모두 채워지면 V2Ray는 `certificateFile`합니다. `keyFile` 및 `키와 동일합니다.`.

{% 힌트 스타일 = '정보'} % 시 `사용량` 인 `'확인'`, 모두 `키 파일` 및 `키` 비어있을 수있다. {% endhint %}

### SockoptObject

```javascript
{
  "mark": 0,
  "tcpFastOpen": false,
  "tproxy": "off"
}
```

> `점`: 숫자

정수. 0이 아니면 값은 소켓 옵션 SO_MARK를 통해 나가는 연결로 설정됩니다. 이 메커니즘은 Linux에만 적용되며 CAP_NET_ADMIN 권한이 필요합니다.

> `tcpFastOpen`: true | 그릇된

[TCP Fast Open](https://en.wikipedia.org/wiki/TCP_Fast_Open)을 사용할지 여부. `설정하면`, V2Ray는 현재 연결에 TFO를 사용합니다. `설정하면`, V2Ray는 TFO를 비활성화합니다. 이 항목이 없으면 V2Ray는 운영 체제의 기본 설정을 사용합니다.

* 다음 운영 체제에만 적용 : 
  * Windows 10 (1604) 이상
  * Mac OS 10.11 / iOS 9 이상
  * Linux 3.16 이상 : 시스템 기본값에 의해 사용 가능합니다.
* 인바운드 및 아웃 바운드 연결에 모두 적용 가능합니다.

> `tproxy`: "redirect"| "tproxy"| "떨어져서"

Linux에서 투명 프록시를 사용할지 여부. 선택 사항은 다음과 같습니다.

* `"off"`: 기본값. TProxy를 전혀 사용하지 마십시오.
* `"redirect"`: 리디렉션 모드로 TProxy를 사용합니다. TCP / IPv4 및 UDP 트래픽을 지원합니다.
* `"tproxy"`: TProxy 모드로 TProxy를 활성화합니다. TCP 및 UDP 트래픽을 지원합니다.

투명한 프록시에는 루트 또는 CAP \ _NET \ _ADMIN 권한이 필요합니다.

{% 힌트 스타일 = '정보'%} 경우 `TProxy` 설정되지 않고, `allowRedirect` 에서 설정 [dokodemo 도어](protocols/dokodemo.md)의 값 `TProxy` 로 설정 될 `"리디렉션"` 자동적. {% endhint %}