
### Các component chính:
"Framework" này có nhiều components, nhưng sẽ có 2 components chính
- `BasePage`
 ```jsx
    import {BasePage} from 'BaseComponent/BasePage'
```
- `BaseConsumer`
```jsx
    import BaseConsumer from 'BaseComponent/BaseConsumer'
```
### Cách sử dụng.
Đối với mỗi trang sẽ tồn tại một Thằng duy nhất được kế thừa từ `BasePage` - tạm gọi là `Cha`, thằng này có nhiệm vụ nắm `state` lớn nhất, lấy dữ liệu và phân phối xuống có các `Con`, các `Con` ở đây là các component nhỏ hơn, được kế thừa từ `BaseConsumer`.
```jsx
    class Con_1 extends BaseConsumer {
        ...
    }
    class Con_2 extends BaseConsumer {
        ...
    }
    class Cha extends BasePage {
        ...
    }
```
Lưu ý: toàn bộ `state` liên quan tới dữ liệu đều phải do thằng `Cha` nắm giữ, các `Con` chỉ có `state` riêng khi `state` đó nhằm mục đích đại loại bật tắt một thứ gì đó, hoặc màu sắc của chữ, ...

### Các phương thức thường hay sử dụng
Nói một cách chuyên sâu thì `BasePage` định nghĩa các hàm/function dùng để chỉnh sửa `state` của nó một cách thuận tiện, và các function này được share cho các `BaseConsumer` nhờ vậy mà tại `Cha` hay tại các `Con` đều có thể sử dụng các function này để chỉnh sửa `state`
Lưu ý: Chỉ có thể sử dụng các hàm này để chỉnh sửa lên `state` của thằng `Cha` (extends from `BasePage`)

`state` mẫu
```jsx
    this.state = {
        people: [
            {name: "Duy", dateOfBirth: "1998"},
            {name: "Be", dateOfBirth: "1999"},
            {name: "De", dateOfBirth: "2000"},
        ]
    }
```

###### 1. updateObject
Hàm này dùng để update một hoặc nhiều trường của một Object  (xuất phát từ `state` của `Cha`), hay nói các khác là merge một object mới vào object cũ
```jsx
    // this.updateObject(oldObject, newObject, callback);
    // VD1: update một trường và có callback
    this.updateObject(this.state.people[0], {name: "Vinh"},()=>{
        console.log("Đã update xong!");
    });
    // VD2: update nhiều trường
    this.updateObject(this.state.people[0], {name: "Mau", dateOfBirth: "2k5"})
```
###### 2. clearListAndPushNewItems
Hàm này dùng để xoá toàn bộ các phần tử của một mảng và thêm các phần từ mới
```jsx
    // this.clearListAndPushNewItems(oldList, newList, callback);
    // VD: 
    this.clearListAndPushNewItems(this.state.people, [{name: "Cuong", dateOfBirth: "2k8"}]);
```
