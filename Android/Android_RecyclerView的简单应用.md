# RecyclerView的简单应用

要使用Recyclerview首先要gradle这个包：

```java
implementation 'com.android.support:design:27.1.1'
```

之后就可以在布局中使用recyclerview这个控件了，布局文件很简单，就是一个控件：

```java
 <android.support.v7.widget.RecyclerView
        android:id="@+id/libaray_recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginTop="4dp"
        android:overScrollMode="never">

    </android.support.v7.widget.RecyclerView>
```

大部分属性和listview作用相同，没啥好讲的，可直接看帮助文件

然后我们写每个item的布局，这个就很随意了，根据不同需要写不同的布局

然后为item写一个类，方便数据传递；

接下来写listadaptor：

```java
public class BookListAdaptor extends RecyclerView.Adapter<BookListAdaptor.ViewHolder> {

    private List<BookItem> bookItemList;

    public BookListAdaptor(List<BookItem> list){
        bookItemList=list;
    }

    public class ViewHolder extends RecyclerView.ViewHolder {
        View bookitemview;//用于设置监听事件
        ImageView bookpic;//这四个是我写的item中的东西
        TextView bookname;
        TextView bookauthor;
        TextView progress_rate;

        public ViewHolder(View itemView) {
            super(itemView);
            bookitemview=itemView;//get the view
            bookpic=itemView.findViewById(R.id.book_pic);//get 实例
            bookname=itemView.findViewById(R.id.book_name);
            bookauthor=itemView.findViewById(R.id.book_author);
            progress_rate=itemView.findViewById(R.id.book_progress_rate);
        }
    }
    @NonNull
    @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view= LayoutInflater.from(parent.getContext()).inflate(R.layout.view_bookitem,parent,false);
        ViewHolder holder=new ViewHolder(view);//加载布局
        //这里可以设置点击事件
         holder.bookitemview.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int position=holder.getAdapterPosition();
                Log.d("DELETE:", String.valueOf(position));
            }
        });
        //我这里设置了两个点击监听，这个是长点击
        holder.bookitemview.setOnLongClickListener(new View.OnLongClickListener() {
            @Override
            public boolean onLongClick(View v) {
                int position=holder.getAdapterPosition();
                DataSupport.deleteAll(BookItem.class,"bookname = ?",bookItemList.get(position).getBookname());
                removeItem(position);
                Log.d("DELETE:", "done! ");
                return false;
            }
        });
        return holder;
        return holder;
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder holder, int position) {
        BookItem bookItem=bookItemList.get(position);//给没每个item赋值
        holder.bookpic.setImageResource(bookItem.getBook_pic_id());
        holder.bookname.setText(bookItem.getBookname());
        holder.bookauthor.setText(bookItem.getAuthor());
        holder.progress_rate.setText(bookItem.getProgress_rate());
    }

    @Override
    public int getItemCount() {
        return bookItemList.size();//获取list长度
    }
    
    //这是一个删除item的函数
     public void removeItem(int postion){
        bookItemList.remove(postion);
        notifyItemRemoved(postion);//用于告知list已经改变了
    }

}
```

Mainactivity中就比较简单了：

Mainactivity：

```java
 		LinearLayoutManager linearLayoutManager=new LinearLayoutManager(getActivity());
        recyclerView.setLayoutManager(linearLayoutManager);
        recyclerView.setAdapter(bookListAdaptor);
```

这个写完是竖直方向的list，若要横向就要加入：

```java
	layoutManager.setOrientation(LinearLayoutManager.HORIZONTAL);
```

若想实现瀑布流就得将layoutManagergen改为StaggeredGridLayoutManager(?,StaggeredGridLayoutManager.VERTICAL);

第一个参数表示列数，第二个表示方向。

>  再改动的过程中，我出现了添加第二行item的时候出现item位置下移，但Recyclerview位置不变，且在添加一定数量的item后所有item就消失了。当讲StaggeredGridLayoutManager改为GridLayoutManager（网格布局）时不会出现这找那个情况。



2018年4月23日19:15:46