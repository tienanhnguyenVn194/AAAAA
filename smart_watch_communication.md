---
# Tài liệu về cách giao tiếp giữa Smartwatch và Smartphone cho ứng dụng Gotip Mobile.

## Nguồn tài nguyên hiện có
+ Ứng dụng đã được hoàn thiện, chạy trên Android và IOS được viết bằng Flutter Framework .
+ Thư viện hỗ trợ giao tiếp giữa Smartphone và Smartwatch, xin check [đường dẫn này](https://pub.dev/packages/flutter_smart_watch) để biết thêm thông tin.

## Nguồn tài nguyên chưa có
+ Ứng dụng WatchOS chạy trên Apple Watch.
+ Ứng dụng WearOS chạy trên WearOS.

## Tổng quan cơ bản về các role user:
Hiện tại ứng dụng đang có 2 role user:
+ User là Creator.
+ User không là Creator.

> Tài liệu giao tiếp này hiện tại được sử dụng cho cả 2 role nhưng chỉ với tư cách là người tham gia online session.

## Hướng dẫn cách giao tiếp
### Sự kiện user trên ứng dụng Mobile tham gia online session của một Creator A

#### Luồng tổng thể:
<img src="./images/user_join_creator_a_live_session_flow.png"/>

#### Những màn hình sẽ diễn ra sự kiện này:

<img src="./images/creator_info.png"/>
<br/>
<img src="./images/donate_history.png"/>

#### Dữ liệu mẫu:

##### Thông tin đã tham gia online session thành công:

```
    {
        "data_type": "join_stream",
        "data":{
            "account": {
                "id": String, // Account ID
                "username": String, // Account username
                "usable_point": Int // The number of points that can be used to donate
            },
            "creator": {
                "id": String, // Creator A's ID
                "avatar": String, // Creator A's avatar
                "username": String, // Creator A's username
                "is_following": Bool // The Boolean flag indicates whether this account follows Creator A.
            },
            
        }
    }
```


##### Thông tin lịch sử donate của online session:

```
    {
        "data_type": "donate_history",
        "data": [
            {
                "id": String, // Donate ID 
                "Sender": {
                    "id": String, // Sender ID
                    "username": String
                },
                "amount": Int, // The amount of points being used
            }
            ...
        ]
    }
```

### Sự kiện user trên ứng dụng Gotip Mobile donate cho Creator A <a name="mobile_donate_for_creator_a"/>

#### Luồng tổng thể: 

<img src="./images/user_mobile_donate_creator_a_flow.png"/>

#### Những màn hình sẽ diễn ra sự kiện này:

<img src="./images/donate_flow.png"/>
<br/>
<img src="./images/play_power_and_duration.png"/>
<br/>
<img src="./images/donate_history.png"/>
<br/>
<img src="./images/donate_function.png"/>

#### Dữ liệu mẫu:

##### Thông tin lần donate mới nhất: 

```
    {
        "data_type": "user_donate",
        "data": {
            "id": String, // Donate ID 
            "Sender": {
                "id": String, // Sender ID
                "username": String
            },
            "amount": Int, // The amount of points being used
        }
    }
```

##### Thông tin về thời gian và mức năng lượng chạy

<img src="./images/play_power_and_duration.png"/>

```
    {
        "data_type": "play_power_and_duration",
        "data":{
            "power_percent": Int, 
            "duration": Int (in second)
        }
    }    
```

 
### Sự kiện user trên ứng dụng Gotip Smartwatch donate cho Creator A

#### Luồng tổng thể:

<img src="./images/user_smart_watch_donate_creator_a_flow.png"/>

#### Dữ liệu mẫu: 

##### Thông tin từ phía Smartwatch gửi lên khi user tiến hành Donate trên smartwatch:

```
    {
        "data_type": "on_donate",
        "data": {
            "amount": Int
        }
    }
```

##### Thông tin từ phía Mobile trả về khi POST "/api/streams/$liveId/tips" thành công: 

```
    {
        "data_type": "donate_result",
        "data": {
            "success": 1
        }
    }
```

##### Thông tin từ phía Mobile trả về khi POST "/api/streams/$liveId/tips" thất bại: 

```
    {
        "data_type": "donate_result",
        "data": {
            "success": 0,
            "cause": "Some error happened"
        }
    }
```

> Các thông tin khác giống với mục [Sự kiện user trên ứng dụng Gotip Mobile donate cho Creator A](#mobile_donate_for_creator_a)

### Sự kiện user trên ứng dụng Gotip Smartwatch mua điểm

> Cần bàn lại về độ khả thi.

### Sự kiện user trên ứng dụng Gotip Mobile theo dõi Creator A <a name="mobile_follow_creator_a"/>

#### Luồng tổng thể:

<img src="./images/mobile_follow_or_unfollow_creator_a_flow.png"/>

#### Những màn hình sẽ diễn ra sự kiện này:

<img src="./images/creator_info.png"/>

#### Dữ liệu mẫu: 

##### Thông tin trạng thái follow hoặc unfollow:

```
    {
        "data_type": "follow_or_unfollow",
        "data": {
            "user_id": String, // The creator A ID
            "is_following": Bool // The Boolean flag indicates whether this account follows Creator A.
        }
    }
```

### Sự kiện user trên ứng dụng Gotip Smartwatch theo dõi Creator A

#### Luồng tổng thể:

<img src="./images/smartwatch_follow_or_unfollow_creator_a_flow.png"/>

#### Dữ liệu mẫu:
Thông tin từ phía Smartwatch gửi lên khi user tiến hành follow Creator A trên smartwatch:

```
    {
        "data_type": "on_follow",
        "data": {
            "creator_id": String
        }
    }
```

Thông tin từ phía Mobile trả về khi POST/DELETE  "/api/users/me/followings" thành công:

```
    {
        "data_type": "follow_or_unfollow",
        "data": {
            "success": 1,   
            "user_id": String, // The creator A ID
            "is_following": Bool // The Boolean flag indicates whether this account follows Creator A.
        }
    }
```

Thông tin từ phía Mobile trả về khi POST/DELETE  "/api/users/me/followings" thất bại:

```
    {
        "data_type": "follow_or_unfollow",
        "data": {
            "success": 0,      
            "cause": "Some error happened"
        }
    }
```

> Các thông tin khác giống với mục [Sự kiện user trên ứng dụng Gotip Mobile theo dõi Creator A](#mobile_follow_creator_a)

### Sự kiện user sử dụng app Mobile và dừng luồng donate
#### Luồng tổng thể:
<img src="./images/mobile_stop_donate_flow.png"/>

#### Những màn hình sẽ diễn ra sự kiện này:
<img src="./images/donate_flow.png"/>

#### Dữ liệu mẫu:
Thông tin user trên mobile đã ngừng quá trình donate:
```
    {
        "data_type": "stop_donate"
    }
```

### Sự kiện user sử dụng Smartwatch và dừng luồng donate
#### Luồng tổng thể:
<img src="./images/smartwatch_stop_donate_flow.png"/>

#### Những màn hình sẽ diễn ra sự kiện này:
<img src="./images/donate_flow.png"/>

#### Dữ liệu mẫu:
Thông tin user trên mobile đã ngừng quá trình donate:
```
    {
        "data_type": "stop_donate"
    }
```


### Sự kiện Creator A bị ngắt kết nối hết tất cả thiết bị Bluetooth
#### Luồng tổng thể:
<img src="./images/creator_all_devices_disconnected_flow.png"/>

#### Những màn hình sẽ diễn ra sự kiện này:
<img src="./images/donate_flow.png"/>
<br/>
<img src="./images/donate_function.png"/>

#### Dữ liệu mẫu:
Thông tin tất cả các thiết bị đều bị ngắt kết nối hoặc có thiết bị kết nối lại:
```
    {
        "data_type": "creator_devices",
        "data": {
            "has_devices": Boolean
        }
    }
```

#### Những điều cần lưu ý: 
+ Nếu `has_devices` là `true`, Gotip User có thể sử dụng chức năng donate trên smartwatch và ngược lại.

### Sự kiện user trên ứng dụng Mobile rời khỏi online session của Creator A
#### Luồng tổng thể: 
<img src="./images/user_leave_live_session_flow.png"/>

#### Những màn hình sẽ diễn ra sự kiện này:

<img src="./images/creator_info.png"/>

#### Dữ liệu mẫu: 

##### Thông tin đã rời khỏi online session thành công:

```
    {
        "data_type": "leave_stream"
    }
```

---


