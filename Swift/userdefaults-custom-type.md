## UserDefaults에 커스텀 타입 저장

Swift의 UserDefaults에 커스텀 타입을 저장하려고 하면 컴파일 에러가 발생한다.

```swift
struct User {
  let name: String
}

@IBAction func saveUser( sender: Any) {
  let me: User = User(name: "sean")
  UserDefaults.standard.set(me, forKey: "me")
  // [User Defaults] Attempt to set a non-property-list object UserDefaults.User...
}
```

UserDefaults에 커스텀 타입을 저장하기 위해서는 `Codable` 프로토콜을 채택한다.

```swift
struct User: Codable {
  let name: String
}
```

`JSONEncoder`를 이용해서 인코딩한 데이터를 저장한다.

```swift
let me: User = User(name: "sean")
let encoder: JSONEncoder = JSONEncoder()

if let encodedMe = try? encoder.encode(me) {
  UserDefaults.standard.set(encodedMe, forKey: "me")
}
```

`JSONDecoder`를 이용해서 데이터를 커스텀 타입으로 디코딩 한다.

```swift
let decoder: JSONDecoder = JSONDecoder()

if let data = UserDefaults.standard.object(forKey: "me") as? Data,
   let decodedMe = try? decoder.decode(User.self, from: result) {
  // ...
}
```