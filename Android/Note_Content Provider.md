# Content Provider

> Android提供的专用于不同应用间进行数据共享的方式。和Massager一样，底层有Binder实现，其使用比ADIL简单，因为这是封装好的。



系统预置了很多ContentProvider，比如通讯录，日历表等，只需要通过ContentProvider的query、update、insert和delete方法即可。



首先，创建一个ContentProvider，并实现6个抽象方法即可：onCreate、query、update、insert、delete和getType。getType用于返回一个Uri请求所对应的MIME类型，如果不关注，直接返回null或"\*/*"



ContentProvider主要以表格的形式组织数据，并且可以有多个表，对每个表格来说，都具有行和列的层次性，行往往对应一条记录，而列对应一条条记录中的一个字段，类似数据库。出了表格形式，ContentProvider还支持文件数据，比如图片、视频、等。文件数据和表格数据的结构不同，因此处理这类数据可以在ContentProvider中的文件的句柄给外界从而让文件来访问ContentProvider中的文件信息。

```java
public class BookProvider extends ContentProvider{
    
    private static final String TAG="BookProvider";

    @Override
    public boolean onCreate() {
        Log.d(TAG, "onCreate()");
        return false;
    }

    @Nullable
    @Override
    public Cursor query(@NonNull Uri uri, @Nullable String[] projection, @Nullable String selection, @Nullable String[] selectionArgs, @Nullable String sortOrder) {
        Log.d(TAG, "query()");
        return null;
    }

    @Nullable
    @Override
    public String getType(@NonNull Uri uri) {
        Log.d(TAG, "getType()" );
        return null;
    }

    @Nullable
    @Override
    public Uri insert(@NonNull Uri uri, @Nullable ContentValues values) {
        Log.d(TAG, "insert()" );
        return null;
    }

    @Override
    public int delete(@NonNull Uri uri, @Nullable String selection, @Nullable String[] selectionArgs) {
        Log.d(TAG, "delete()");
        return 0;
    }

    @Override
    public int update(@NonNull Uri uri, @Nullable ContentValues values, @Nullable String selection, @Nullable String[] selectionArgs) {
        Log.d(TAG, "update()");
        return 0;
    }
}
```

query返回一个Cursor对象，query参数和SQLiteDatabase中相似如下：

| query()方法参数 |        对应SQL部分        |               描述               |
| --------------- | :-----------------------: | :------------------------------: |
| uri             |      from table_name      | 指定查询某个应用程序下的某一张表 |
| projection      | selection column1,column2 |          指定查询的列名          |
| selection       |    where column==value    |       指定where的约束条件        |
| selectionArgs   |             -             |  为where中的占位符踢动具体的值   |
| sortOrder       | order by column1,column2  |      指定查询结果的排序方式      |



我们需要注册这个BookProvider。

```java
 /*
            android:authorities必须唯一标识，建议加上包名前缀；
            为了演示进程间通信，让Bookprovider运行在独立的进程，并加上权限
            这样外界想访问BookProvider就必须声明权限
            enable表示是否启动内容提供器
            exported表示是否允许外部程序访问内容提供器
        */
        <provider
            android:permission="com.example.PROVIDER"
            android:authorities="com.example.peanut.book.provider"
            android:name=".util.BookProvider"
            android:process=":provider"
            android:enabled="true"
            android:exported="true">
        </provider>
```

为了方便，演示在同一个应用中进行，独立的应用访问道理是一样的。

```
public class MainActivity extends AppCompatActivity {


    private static final String TAG="Mainactivity";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Uri uri=Uri.parse("content://com.example.peanut.book.provider");

        Cursor cursor=getContentResolver().query(uri,null,null,null,null);
        Log.d(TAG, "onCreate()");
    }
}
```

在activity中通过Cursor对象来获取数据信息。