# iOS vs Android

本仓库主要收集一些 Android 和 iOS 在实现同样功能点的共同点和差异点。由于两个平台毕竟实际差异很大，所以只是把功能近似的归类在一起，放在一起的并不表示等于。

正文中的标题格式如下：

iOS 端实现 ~~ Android 端实现

例如： `Controller ~~ Activity`，就表示 iOS 端的 Controller 相当于 Android 端的 Activity。

## 基本功能

### Controller ~~ Activity

#### iOS 平台

初始化 Controller

Object C

```oc
MainViewController *controller = [[MainViewController alloc]init];
```

Swift

```swift
 let controller = MainViewController()
```

#### Android 平台

由系统的 ActivityManager 负责执行初始化，应用层无法创建 Activity 实例

### 设置初始画面和系统背景色

#### iOS 平台

在 `AppDelegate.m` 中设置 `root controller`

 Object C

```oc
self.window.rootViewController = myController;
self.window.backgroundColor = [UIColor whiteColor];
[self.window makeKeyAndVisible];
```

Swift

```swift
self.window?.rootViewController = myController
self.window?.backgroundColor = UIColor.white
self.window?.makeKeyAndVisible()
```

#### Android 平台

在 `AndroidMainifest.xml` 为入口 Activity 添加如下 `intent-filter`

```xml
<intent-filter>
  <action android:name="android.intent.action.MAIN" />
  <category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
```

为入口 Activity 设置 `theme` 或者样式

### View 作为成员变量

#### iOS 平台

需要声明为 `IBOutlet`，建立连接，并且除非是根布局，否则通常都声明为弱引用

Object C

声明在类的扩展中

```oc
@interface ReminderViewController()
@property (nonatomic, weak) IBOutlet UIDatePicker *datePicker;
@end
```

Swift

```swift
 @IBOutlet weak var datePicker:UIDatePicker?
```

#### Android 平台

在对应的类中声明变量，然后在生命周期函数中通过 `findViewById()` 进行初始化

```java
private DatePicker datePicker;

datePicker = (DatePicker)findViewById(R.id.dp);
```



### 静态变量

#### iOS 平台

Object C

直接使用 `static` 声明在方法内部

```oc
static NSDateFormatter *dateFormatter = nil;
if(!dateFormatter){
    dateFormatter = [[NSDateFormatter alloc]init];
}
```

Swift

```swift
static let dateFormatter:DateFormatter = {
    let formatter = DateFormatter()
    return formatter
}()
```

Android

```java
private static DateFormat dateFormat = new DateFormat();
```

### 触摸点击事件

#### iOS 平台

点击等事件可以声明为 `IBAction`，其它事件需要实现对应的 delegate

点击事件

Object C

```oc
-(IBAction)addReminder:(id)sender{
}
```

Swift

```oc
@IBAction func addReminder(_ sender: Any) {
}
```

其它事件

Object C

```oc
@interface MyViewController()<UITextFieldDelegate>
@end

@implementation MyViewController
- (void)loadView{
  textField.delegate = self;
}
- (BOOL)textFieldShouldReturn:(UITextField *)textField{
}
@end
```

Swift

```swift
class HypnosisViewController: UIViewController,UITextFieldDelegate {
  func textFieldShouldReturn(_ textField: UITextField) -> Bool {
  }
}
```

Android

点击事件

```java
button.setOnClickListener(new View.OnClickListener() {
  @Override
  public void onClick(View v) {    
  }
});
```

其它事件则实现对应的监听器


## 生命周期

### 布局加载完毕

#### iOS 平台

一般只有第一次加载时需要应用的代码写在这里

Object C

```oc
-(void)viewDidLoad{
    [super viewDidLoad];
}
```

Swift

```swift
override func viewDidLoad() {
    super.viewDidLoad()
}
```

Android

```java
 protected void onCreate(Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
 }
```

### 布局可见/不可见

#### iOS 平台

一般每次都需要使用的代码写在这里

Object C

```oc
-(void)viewWillAppear:(BOOL)animated{
    [super viewWillAppear:animated];
}
-(void)viewWillDisappear:(BOOL)animated{
    [super viewWillDisappear:animated];
}
```

swift

```swift
override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
}
override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)
}
```

Android

```java
@Override
protected void onResume() {
    super.onResume();
}
@Override
protected void onPause() {
    super.onPause();
}
```

### 重绘画面

#### iOS 平台

Object C

```oc
[self setNeedsDisplay];
```

Swift

```swift
self.setNeedsDisplay()
```

#### Android 平台

```java
this.invalidate();
```

#### 设置画面布局

##### iOS 平台

重写 `loadView()` 方法

Object C

```oc
-(void)loadView{
    self.view = backgroundView;
}
```

Swift

```swift
override func loadView() {
}
```

##### Android 平台

```java
setContentView(view);
```

### 画面间传递参数

#### iOS 平台

直接修改取得的 `ViewController` 的成员变量

#### Android 平台

通过 Intent 对象进行传递

```java
Intent intent = new Intent();
intent.setClass(this, SecondActivity.class);
intent.put("x", 1);
startActivity(intent);
```



### 格式化日期

#### iOS 平台

Object C

```oc
NSDateFormatter *dateFormatter = [[NSDateFormatter alloc]init];
dateFormatter.dateStyle = NSDateFormatterMediumStyle;
dateFormatter.timeStyle = NSDateFormatterNoStyle;
self.dateLabel.text = [dateFormatter stringFromDate:item.dateCreated];
```

Swift

```swift
let formatter = DateFormatter()
formatter.dateStyle = .medium
formatter.timeStyle = .none
dateLabel.text = formatter.string(from: item!.dateCreated)
```

#### Android 平台

```java
 DateFormat dateFormat = new SimpleDateFormat(dateStyle, Locale.US);
 tvDate.setText(dateFormat.format(item.getDateCreated()));
```


## 布局

### UINavigationController ~~ ToolBar

#### 显示在画面上

##### iOS 平台

在 `AppDelegate` 中创建 `UINavigationController` 的实例并作为根控制器
`UINavigationController` 中保存着画面栈，且初始化时需传入最为栈底的控制器，只有栈顶的控制器所对应的画面可见

Object C

```oc
UINavigationController *navController = [[UINavigationController alloc]initWithRootViewController:ivc];
self.window.rootViewController = navController;
```

Swift

```swift
let navController = UINavigationController(rootViewController: itemsVC)
window?.rootViewController = navController
```

##### Android 平台

添加在 xml 中

```xml
    <android.support.v7.widget.Toolbar
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:background="@color/colorPrimary" />
```

#### 设置标题文字

##### iOS 平台

在每个 `ViewController` 中都可以通过成员变量 `navigationItem` 修改 `UINavigationController` 上的显示效果

Object C

```oc
navigationItem.title=_item.itemName;
```

Swift

```swift
navigationItem.title = it?.itemName
```

##### Android 平台

```java
toolbar.setTitle(itemName)
```

#### 路由跳转

##### iOS 平

通过每个 `ViewController` 的 `navigationController` 实例可以控制画面跳转

Object C

```oc
[navigationController pushViewController:detailViewController animated:YES];
```

Swift

```swift
navigationController?.pushViewController(detailViewController, animated: true)
```

Android

使用 Intent

### UINavigationItem,UIBarButtonItem ~~ View

##### iOS 平台

`UINavigationItem` 是 `UINavigationController` 上的显示项，可以通过它修改标题文字，添加左右按钮

Object C

```oc
UINavigationItem *navItem = self.navigationItem;

// 设置标题文字
navItem.title = @"Homepwner";

// 设置右按钮为自定义方法，方法名为 addNewItem
UIBarButtonItem *addButtonItem = [[UIBarButtonItem alloc]initWithBarButtonSystemItem:UIBarButtonSystemItemAdd target:self action:@selector(addNewItem:)];
navItem.rightBarButtonItem = addButtonItem;

// 设置左按钮为系统消息，消息为 editButtonItem
navItem.leftBarButtonItem = self.editButtonItem;
```

Swift

```swift
let navItem = navigationItem

// 设置标题文字
navItem.title="Homepwer - Swift"

// 设置右按钮为自定义方法，方法名为 addNewItem
let addButton = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: "addNewItem:")
navItem.rightBarButtonItem = addButton
navItem.leftBarButtonItem = editButtonItem

// 设置左按钮为系统消息，消息为 editButtonItem
navItem.leftBarButtonItem = editButtonItem
```

##### Android 平台

重写 `onCreateOptionsMenu(Menu menu)` 添加对应菜单，如果希望完全自定义的话则需要直接在 xml 里在 ToolBar 节点中添加需要的子组件。

## 组件

### UITextField ~~ TextView

#### 设置文字

##### iOS  平台

Object C

```oc
textField.text = item.itemName;
```

Swift

```swift
textField.text = item.itemName
```

##### Android 平台

```java
 textview.setText(item.itemName);
```

#### 设置 PlaceHolder

##### iOS 平台

Object C

```oc
textField.placeholder = @"Hypnotize me";
```

Swift

```swift
textField.placeholder = "Hypno me"
```

##### Android 平台

```java
textView.setHint("Hypno me");
```


#### 设置 Return Key Type ~~ Action Type

用于表示键盘右下角应该显示什么键

##### iOS 平台

Object C

```oc
textField.returnKeyType = UIReturnKeyDone;
```

Swift

```swift
textField.returnKeyType = UIReturnKeyType.done
```

##### Android 平台

一般直接在布局文件中设置，也可以像以下方式在代码中设置

```java
textView.setImeOptions(EditorInfo.IME_ACTION_DONE);
```

#### 响应点击 Return 事件

##### iOS 平台

 需要实现 `UITextFieldDelegate` 委托

Object C

```oc
- (BOOL)textFieldShouldReturn:(UITextField *)textField{
}
```

Swift

```swift
func textFieldShouldReturn(_ textField: UITextField) -> Bool {
}
```

###### Android 平台

```java
textView.addTextChangedListener(new TextWatcher() {
    @Override
    public void beforeTextChanged(CharSequence s, int start, int count, int after) {

    }

    @Override
    public void onTextChanged(CharSequence s, int start, int before, int count) {

    }

    @Override
    public void afterTextChanged(Editable s) {

    }
});
```

#### 关闭键盘

##### iOS 平台

Object C

```oc
[textField resignFirstResponder];
```

Swift

```swift
textField.resignFirstResponder()
```

##### Android 平台

```java
InputMethodManager imManager = (InputMethodManager) getSystemService(Context.INPUT_METHOD_SERVICE);  
imManager.hideSoftInputFromWindow(view.getWindowToken(), 0);
```

### UITableViewController ~~ ListView

#### 设置基本样式

##### iOS 平台

Object C

```oc
-(instancetype)init{
    self = [super initWithStyle:UITableViewStylePlain];
    return self;
}
```

Swift

```swift
init() {
    super.init(style: UITableViewStyle.plain)
}
```

##### Android 平台

通过对应的 Adapter 进行设置

```java
ArrayAdapter adapter = new ArrayAdapter<>(this, android.R.layout.simple_list_item_1,
                android.R.id.text1, items);
```

#### 设置需要显示额行数

##### iOS 平台

Object C

```oc
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section{
    return [[[ItemStore sharedStore]allItems]count];
}
```

 Swift

```swift
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return ItemStore.sharedStore().allItems.count
}
```

##### Android 平台

重写 Adapter 的方法

```java
@Override
public int getCount() {
    return items.length;
}
```

#### 设置单元格内容

##### iOS 平台

Object C

```oc
-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath{
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"UITableViewCell" forIndexPath:indexPath];
    BNRItem *item = items[indexPath.row];
    cell.textLabel.text = [item description];
    return cell;
}

-(void)viewDidLoad{
    [super viewDidLoad];
    [self.tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:@"UITableViewCell"];
}
```

Swift

```swift
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCell(withIdentifier: "UITableViewCell", for:indexPath)
    let item = items[indexPath.row]
    cell.textLabel?.text = item.description
    return cell
}

override func viewDidLoad() {
    super.viewDidLoad()   
    tableView.register(UITableViewCell.self, forCellReuseIdentifier: "UITableViewCell")
}
```

##### Android 平台

```java
@Override
public View getView(int position, View convertView, ViewGroup parent) {
    ViewHolder viewHolder;
    if (null == convertView) {
        convertView = LayoutInflater.from(context).inflate(android.R.layout.simple_list_item_1, null);
        viewHolder = new ViewHolder(convertView);
        convertView.setTag(viewHolder);
    } else {
        viewHolder = (ViewHolder) convertView.getTag();
        if (null == viewHolder) {
            viewHolder = new ViewHolder(convertView);
            convertView.setTag(viewHolder);
        }
    }
    viewHolder.label.setText(items[position]);
    return convertView;
}

class ViewHolder {
    private TextView label;

    ViewHolder(View view) {
        this.label = (TextView) view.findViewById(android.R.id.text1);
    }
}
```

#### 添加新行

##### iOS平台

Object C

```oc
NSIndexPath *indexPath = [NSIndexPath indexPathForRow:lastRow inSection:0];
[self.tableView insertRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationTop];
```

Swift

```swift
tableView.insertRows(at: [indexPath], with: UITableViewRowAnimation.top)
```

##### Android 平台

修改 Adapter 使用的列表实例，添加一行，并调用 `adapter.notifyDataSetChanged();`

#### 编辑模式

iOS 平台

Object C

```oc
if(self.isEditing){
    [sender setTitle:@"Edit" forState:UIControlStateNormal];
    //关闭编辑模式
    [self setEditing:NO animated:YES];
}else{
    [sender setTitle:@"Done" forState:UIControlStateNormal];
    [self setEditing:YES animated:YES];
}

// 删除
- (void)   tableView:(UITableView *)tableView
  commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
   forRowAtIndexPath:(NSIndexPath *)indexPath
{
    // If the table view is asking to commit a delete command...
    if (editingStyle == UITableViewCellEditingStyleDelete) {
        [tableView deleteRowsAtIndexPaths:@[indexPath]
                         withRowAnimation:UITableViewRowAnimationFade];
    }
}

// 排序
- (void)   tableView:(UITableView *)tableView
  moveRowAtIndexPath:(NSIndexPath *)sourceIndexPath
         toIndexPath:(NSIndexPath *)destinationIndexPath
{
    [[ItemStore sharedStore] moveItemAtIndex:sourceIndexPath.row
                                        toIndex:destinationIndexPath.row];
}
```

Swift

```swift
if isEditing {
    sender.setTitle("Edit", for: UIControlState.normal)
    // 关闭编辑模式
    setEditing(false, animated: true)
}else{
    sender.setTitle("Done", for: UIControlState.normal)
    setEditing(true, animated: true)
}

// 删除
override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCellEditingStyle, forRowAt indexPath: IndexPath) {
    if editingStyle == UITableViewCellEditingStyle.delete{
        let items = ItemStore.sharedStore().allItems
        let item=items[indexPath.row]
        ItemStore.sharedStore().removeItem(item: item)
        tableView.deleteRows(at: [indexPath], with: UITableViewRowAnimation.fade)
    }
}

// 排序
override func tableView(_ tableView: UITableView, moveRowAt sourceIndexPath: IndexPath, to destinationIndexPath: IndexPath) {
    ItemStore.sharedStore().moveItemAtIndex(fromIndex: sourceIndexPath.row, toIndex: destinationIndexPath.row)
}
```

Android 平台

##### 完全自己实现


#### 刷新数据

##### iOS 平台

Object C

```oc
[self.tableView reloadData];
```

Swift

```swift
tableView.reloadData()
```

##### Android 平台

```java
adapter.notifyDataSetChanged();
```

#### 选中行的回调

OS

Object C 平台

```oc
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath{
}

```

Swift

```swift
override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
 }
```

##### Android 平台

```java
listView.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
    @Override
    public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {

    }

    @Override
    public void onNothingSelected(AdapterView<?> parent) {

    }
});
```
