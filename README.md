
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

### Các phương thức chỉnh sửa `state` thường hay sử dụng
Nói một cách chuyên sâu thì `BasePage` định nghĩa các hàm/function dùng để chỉnh sửa `state` của nó một cách thuận tiện, và các function này được share cho các `BaseConsumer` nhờ vậy mà tại `Cha` hay tại các `Con` đều có thể sử dụng các function này để chỉnh sửa `state`
Lưu ý 1: Chỉ có thể sử dụng các hàm này để chỉnh sửa lên `state` của thằng `Cha` (extends from `BasePage`)

`state` mẫu
```jsx
    this.state = {
        people: [
            {name: "Duy", dateOfBirth: "1998"},
            {name: "Be", dateOfBirth: "1999"},
            {name: "De", dateOfBirth: "2000"},
        ],
        person: {
            name: "John",
            age: 21,
            address: "Hcmc",
        }
    }
```
###### 1. updateObject
Hàm này dùng để update một hoặc nhiều trường của một Object  (xuất phát từ `state` của `Cha`), hay nói các khác là merge một object mới vào object cũ
```jsx
    // this.updateObject(oldObject, newObject, callback);
    // VD1: update một trường và có callback
    this.updateObject(this.state.person, {name: "Johnson"},()=>{
        console.log("Đã update xong!");
    });
    // VD2: update nhiều trường 
    this.updateObject(this.state.person, {name: "Johnson", age: 25})
    // VD3: update thêm trường
    this.updateObject(this.state.person, {name: "Johnson", gender: "male"}, ()=>{
        // lúc này this.state.person ~~ {
        //    name: "Johnson",
        //    age: 21,
        //    address: "Hcmc",
        //    gender: "male"
        //}
    })
```
###### 2. clearListAndPushNewItems
Hàm này dùng để xoá toàn bộ các phần tử của một mảng và thêm các phần từ mới
```jsx
    // this.clearListAndPushNewItems(oldList, newList, callback);
    // VD: 
    const newList = [
        {
            name: "Cuong", 
            dateOfBirth: "2k8"
        }, 
        {
            name: "Tran", 
            dateOfBirth: "1985"
        }
    ];
    this.clearListAndPushNewItems(this.state.people, newList);
```
###### 3. addElement
Thêm một phần tử vào cuối, hoặc vào một vị trí cụ thể của list nếu truyền vào index
```jsx
    // this.addElement(listObj, obj, index, callback);
    // index là vị trí add vào, để null thì default sẽ add vào sau cùng
    // VD: 
    const newPerson = {
        name: "Cuong", 
        dateOfBirth: "2k8"
    };
    this.addElement(this.state.people, newPerson);
```
###### 4. removeElement
Xoá một phần từ khỏi List dựa vào reference (KHÔNG phải giá trị)
```jsx
    // this.removeElement(listObj, obj, callback);
    // VD: 
    this.removeElement(this.state.people, this.state.people[0]);
```

###### 5. mergeList
Ghép một 1 list mới vào 1 list cũ từ state theo điều kiện
Cú pháp:
```jsx
    this.mergeList(oldList, newList, comparer, callback);
```
VD: 
```jsx
    this.state.list = [
       {id: 1, name: "John"},
       {id: 2, name: "Jimmy"},
    ];
    var newList = [
       {id: 3, name: "Jane"},
       {id: 1, name: "Josh"},
    ]
    function compareFunc(a, b){
       return a.id == b.id
    } 
    this.mergeList(this.state.list, newList, compareFunc, ()=>{
      // this.state.list lúc này sẽ là
      [
        {id: 1, name: "Josh"},
        {id: 2, name: "Jimmy"},
        {id: 3, name: "Jane"},
      ];
      // explain
      // object {id: 1, name: "John"} đã được merge bởi {id: 1, name: "Josh"} dựa vào comparer (a.id == b.id)
    });
```

### Các hàm hỗ trợ 


###### 6. openModal
Mở một modal và trả về index của modal đó, dùng ở hàm 6
```jsx
    const modalIndex = this.openModal(()=>{
        return({
            title: "title",
            body: <A_Random_Component />, // nội dung
            otherProps: {
                // một số các option cho modal, có thể có hoặc ko 
                slideDirection: "left" // or "right", "down", "up", phía xuất hiện và biến mất của modal
                freeSize: true // false, nếu true thì kích thước modal sẽ tuỳ thuộc vào body
                // options khác 
            }
        })
    });
```

###### 7. closeModal
Đóng một modal dựa vào modalIndex, nếu modalIndex là -1 thì sẽ đóng modal được tạo gần nhất
```jsx
    this.closeModal(modalIndex);
```

###### 8 popup.
```jsx
    // popup thông báo đơn 
    this.success("Thành công"); 
    this.warning("Cảnh báo");
    this.error("Lỗi");
    // confirm
    this.confirm(
        "Are you sure about that?",
        {
            okay: {
                title: "Yes I am", // text hiển thị trên nút oke 
                handle: () => {
                    // hành động thực hiện sau khi nhấn nút oke
                }
            },
            cancel: {
                title: "No I'm not", // text hiển thị trên nút cancel 
                handle: () => {
                    // hành động thực hiện sau khi nhấn nút cancel
                }
            }
        }
    )
```

###### 9. Clone component
Giả sử mở một Modal có một component `EditProduct` được dùng để chỉnh sửa lên một object `product` như bên dưới.
Lúc này việc onChange value bên trong `EditProduct` sẽ chỉnh sửa luôn `this.state.products[0]` ở bên ngoài, và việc này chưa chắc mình đã mong muốn
```jsx
class EditProduct extends BaseConsumer {
    
    consumerContent(){
        const { product } = this.props;
        return(
            <input 
                placeholder="Nhap ten"
                value={product.name}
                onChange={event => {
                    this.updateObject(product, {name: event.target.value});
                }}
            />
        )
    }
}
```
```jsx
const modalFunc = () => (
    <EditProduct 
        product={this.state.products[0]} // ví dụ
    />
)
this.openModal(modalFunc);
```
Cách giải quyết:
1. Tạo một component, thường sẽ thêm chữ "Clone" đăng trước, `CloneEditProduct`
```jsx
class CloneEditProduct extends BaseCloneConsumer {
    _randomKey = ehealth.guid.get();
    getCloneStateName() {
        return this._randomKey;
    }
    getCloneStateData() {
        return this.props.product;
    }
    childrenCloneContent(){
        // data này chính là this.props.product đã được clone ra
        const data = this.getCloneStateByKey(this._randomKey); 
        const { product, ...others } = this.props;
        return (
            <EditProduct 
                commitData={this.commitData} // hàm này dùng để "đập" product đã được clone ra product bên ngoài
                product={data}
                {...others}
            />
        )
    }
}
```
2. Khi muốn bật modal và chỉ muốn chỉnh sửa data bên trong modal đó (không kéo theo data bên ngoài)
```jsx
const modalFunc = () => (
    <CloneEditProduct 
        product={this.state.products[0]}
    />
)
this.openModal(modalFunc);
```
3. Lúc này ở bên trong EditProduct sẽ hoạt động một cách bình thường (ngoài việc khi được chỉnh sửa thì chỉnh sửa một mình nó) và có thêm một hàm là this.props.commitData (hàm này không tự động có, được truyền vào ở `CloneEditProduct`)
```jsx
class EditProduct extends BaseConsumer {
    consumerContent(){
        const { product, commitData } = this.props;
        return(
            <div>
                <input 
                    placeholder="Nhap ten"
                    value={product.name}
                    onChange={event => {
                        this.updateObject(product, {name: event.target.value});
                    }}
                />
                <button onClick={() => {
                        commitData(); // sau hàm này, data nãy giờ chỉnh sửa sẽ cập nhật ra bên ngoài
                    }}>
                    Save
                </button>
            </div>
        )
    }
}
```
