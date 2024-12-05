# my_works
在原有的基础上添加了时间戳显示、搜索的扩展功能。
1.时间戳显示 在笔记列表中显示笔记的修改时间，采用北京时间格式。1.时间戳功能
功能描述：
每条笔记都将在创建或修改时自动记录时间戳，便于用户查看最后修改时间。

实现方法：
在保存笔记时，自动为每个笔记记录当前的时间戳。这个时间戳将会存储在数据库中的 COLUMN_NAME_MODIFICATION_DATE 字段。

代码实现：
在创建或更新笔记时，自动插入当前时间戳：

java

// 在保存笔记时更新时间戳

ContentValues values = new ContentValues();

values.put(NotePad.Notes.COLUMN_NAME_TITLE, title);

values.put(NotePad.Notes.COLUMN_NAME_CONTENT, content);

values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, System.currentTimeMillis()); // 使用当前时间戳

// 插入或更新笔记

getContentResolver().insert(NotePad.Notes.CONTENT_URI, values);

在笔记列表中显示时间戳：

// 定义数据列和视图ID

String[] dataColumns = {

NotePad.Notes.COLUMN_NAME_TITLE,

NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE

};

int[] viewIDs = {

android.R.id.text1,

R.id.time // 时间戳显示控件

};

SimpleCursorAdapter adapter = new SimpleCursorAdapter(

​ this,

​ R.layout.noteslist_item,

​ currentCursor,

​ dataColumns,

​ viewIDs

);

setListAdapter(adapter);
2.笔记搜索功能 支持通过标题和内容搜索笔记，实现了实时搜索功能。功能描述：
用户可以根据标题对笔记进行搜索。

实现方法：
提供一个 Dialog 弹框，让用户输入搜索关键词，通过查询数据库中的笔记标题进行模糊匹配。

代码实现：
启动搜索活动，显示输入框：

// 启动搜索活动

private void startSearchActivity() {

AlertDialog.Builder builder = new AlertDialog.Builder(this);

builder.setTitle("请输入查找内容:");

final EditText input = new EditText(this);

builder.setView(input);

builder.setPositiveButton("确定", new DialogInterface.OnClickListener() {

​ @Override

​ public void onClick(DialogInterface dialog, int which) {

​ searchQuery = input.getText().toString(); // 获取查询内容

​ performSearch();

​ }

});

builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {

​ @Override

​ public void onClick(DialogInterface dialog, int which) {

​ dialog.dismiss();

​ }

});

builder.show();

}

// 执行搜索

private void performSearch() {

if (TextUtils.isEmpty(searchQuery)) {

​ // 如果搜索词为空，显示所有笔记

​ currentCursor = getContentResolver().query(

​ getIntent().getData(),

​ PROJECTION,

​ null, // 不需要过滤条件

​ null,

​ NotePad.Notes.DEFAULT_SORT_ORDER

​ );

} else {

​ // 执行标题模糊搜索

​ String selection = NotePad.Notes.COLUMN_NAME_TITLE + " LIKE ?";

​ String[] selectionArgs = new String[]{"%" + searchQuery + "%"};

​ currentCursor = getContentResolver().query(

​ getIntent().getData(),

​ PROJECTION,

​ selection,

​ selectionArgs,

​ NotePad.Notes.DEFAULT_SORT_ORDER

​ );

}

// 更新数据源，刷新列表

if (currentCursor != null && currentCursor.getCount() > 0) {

​ SimpleCursorAdapter adapter = (SimpleCursorAdapter) getListAdapter();

​ adapter.changeCursor(currentCursor);

} else {

​ currentCursor = null;

​ SimpleCursorAdapter adapter = (SimpleCursorAdapter) getListAdapter();

​ adapter.changeCursor(currentCursor); // 更新为空的数据源

​ Toast.makeText(this, "未找到符合条件的笔记", Toast.LENGTH_SHORT).show(); // 提示未找到结果

}

}
# 使用说明
启动应用

点击"+"添加新笔记

长按笔记可以进行编辑、删除等操作

点击菜单可以进行搜索等操作

