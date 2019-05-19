# 基于NotePad的期中作业

## 基本功能实现：

### 一：显示时间戳
### 二：笔记查询功能

## 扩展功能实现：

### 三：更换背景颜色





### 一：显示时间戳
* 功能截图: 

<div align=center>
<img src="/imgs/事件最后编辑时间戳.png" width = "400" height = "800" div align=center />
</div>

#### 在noteList_item中添加一个TextView来显示时间

<?xml version="1.0" encoding="utf-8"?>
<!-- Copyright (C) 2010 The Android Open Source Project

     Licensed under the Apache License, Version 2.0 (the "License");
     you may not use this file except in compliance with the License.
     You may obtain a copy of the License at
  
          http://www.apache.org/licenses/LICENSE-2.0
  
     Unless required by applicable law or agreed to in writing, software
     distributed under the License is distributed on an "AS IS" BASIS,
     WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     See the License for the specific language governing permissions and
     limitations under the License.
-->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
android:layout_height="wrap_content"
android:orientation="vertical"
android:padding="10dp">

    <TextView
    android:text="Title"
    android:id="@android:id/text1"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textColor="@android:color/white"
    android:textSize="18sp"
    tools:ignore="HardcodedText" />

<TextView
    android:text="Sub Title"
    android:id="@+id/listItem_sub_title"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textColor="@android:color/darker_gray"
    android:textSize="13sp"
    tools:ignore="HardcodedText" />

</LinearLayout>

### 二：笔记查询功能
* 功能截图: 
<div align=center>
<img src="/imgs/搜索之前.png" width = "400" height = "800" div align=center />
<img src="/imgs/模糊搜索功能.png" width = "400" height = "800" div align=center />
    
</div>



#### 在noteList_listview布局里 添加搜索控件

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="8dp"
    android:paddingRight="8dp">

    <SearchView
        android:id="@+id/search_notes"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:queryHint="搜索便签" />

    <ListView
        android:id="@android:id/list"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:background="@android:color/holo_green_dark"
        android:drawSelectorOnTop="false" />

    <TextView
        android:id="@android:id/empty"
        android:gravity="center"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:background="@android:color/holo_blue_dark"
        android:text="无搜索结果" />
</LinearLayout>
 
#### 在对应activity里 添加搜索控件对应的控制代码

    /**
     * 这是一个自定义的方法，用于封装
     * 初始化搜索组件的相关代码。
     */
    @TargetApi(Build.VERSION_CODES.ICE_CREAM_SANDWICH)
    private void initializeSearchView(final SimpleCursorAdapter adapter) {
        SearchView searchView = (SearchView) findViewById(R.id.search_notes);
        searchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
            @Override
            public boolean onQueryTextSubmit(String query) {
                return false;
            }

            @Override
            public boolean onQueryTextChange(String newText) {
                Cursor newCursor;
                // Copy from smartflowers↓
                if (newText != null && !newText.trim().isEmpty()) {
                    String selection = NotePad.Notes.COLUMN_NAME_TITLE + " GLOB '*" + newText + "*'";
                    newCursor = getContentResolver().query(
                            getIntent().getData(),
                            PROJECTION,
                            selection,
                            null,
                            NotePad.Notes.DEFAULT_SORT_ORDER
                    );
                } else {
                    newCursor = getContentResolver().query(
                            getIntent().getData(),
                            PROJECTION,
                            null,
                            null,
                            NotePad.Notes.DEFAULT_SORT_ORDER
                    );
                }
                adapter.swapCursor(newCursor); // 视图将同步更新！
                return true;
            }
        });
    }
    
### 三：更换背景颜色
* 功能截图: 
<div align=center>
<img src="/imgs/修改背景颜色.png" width = "400" height = "800"  />
<img src="/imgs/单选框修改背景颜色.png" width = "400" height = "800"  />
<img src="/imgs/修改背景颜色成功.png" width = "400" height = "800" />
</div>


#### 在options_menu里 添加修改背景颜色选项item

    <item android:id="@+id/menu_changeColor"
        android:icon="@drawable/ic_menu_compose"
        android:title="@string/menu_changeColor" />

#### 在对应activity里 添加修改背景颜色的相关代码

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.menu_add:
                /*
                 * Launches a new Activity using an Intent. The intent filter for the Activity
                 * has to have action ACTION_INSERT. No category is set, so DEFAULT is assumed.
                 * In effect, this starts the NoteEditor Activity in NotePad.
                 */
                startActivity(new Intent(Intent.ACTION_INSERT, getIntent().getData()));
                return true;
            case R.id.menu_paste:
                /*
                 * Launches a new Activity using an Intent. The intent filter for the Activity
                 * has to have action ACTION_PASTE. No category is set, so DEFAULT is assumed.
                 * In effect, this starts the NoteEditor Activity in NotePad.
                 */
                startActivity(new Intent(Intent.ACTION_PASTE, getIntent().getData()));
                return true;
            case R.id.menu_changeColor:
                new AlertDialog.Builder(this).setTitle("请选择背景颜色") .setSingleChoiceItems(new String[]{"黄色", "蓝色", "紫色", "绿色"}, -1, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {

                    }
                })
                        .setPositiveButton("确定",null)
                        .show();
            default:
                return super.onOptionsItemSelected(item);
        }
    }
    
### 其余截图:
   *  添加新事件截图: 
<div align=center>
  
<img src="/imgs/添加新事件功能.png" width = "400" height = "800" div align=center />
</div>

   *  修改事件标题截图: 
<div align=center>
       
<img src="/imgs/修改事件标题功能.png" width = "400" height = "800" div align=center />
</div>



