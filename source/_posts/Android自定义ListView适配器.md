---
title: Android自定义ListView适配器
date: 2016-01-15 09:35:12
tags: [Android, 安卓开发]
---
&emsp;&emsp;安卓自定义ListView的的外观以后，我们无法使用自带的simpleAdapter去为其绑定适配器了，因此我们就需要自己写一个适配器来绑定。  
&emsp;&emsp;这里我们把一个ListItem的样式设置为右侧头像左侧文字的样式。  

<!-- more -->

&emsp;&emsp;首先我们先新建一个layout，命名为chatlist_item_left.xml，在里面设置好一个头像，然后左侧设置为一个TextView，代码如下：

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="horizontal"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

    <TextView
        android:id="@+id/item_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="24sp" />

    <ImageView
        android:id="@+id/item_icon"
        android:layout_width="36dp"
        android:layout_height="36dp"
        android:src="@drawable/ic_launcher"
        android:contentDescription="@string/avatar" />

    </LinearLayout>

&emsp;&emsp;这样子的效果如下图：  
![Alt text](http://ww1.sinaimg.cn/mw690/7c9c3ec6jw1f02mavkm3ej20db01qa9z.jpg "Optional title")  
&emsp;&emsp;然后只需要写好适配器就可以了。  
&emsp;&emsp;适配器是继承于BaseAdapter类的。然后需要重写它的一些基本函数，同时我们需要再其getView()函数中动态载入我们写好的自定义layout。  
&emsp;&emsp;代码如下：

    package com.tjurobocup.learningexample;

    import android.content.Context;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import android.widget.BaseAdapter;
    import android.widget.TextView;

    import java.util.ArrayList;

    public class ChatListAdapter extends BaseAdapter {
        private ArrayList<String> list;
        private Context context;
        private int chatListLayout;

        public ChatListAdapter(Context context, ArrayList<String> list, int chatListLayout) {
            this.context = context;
            this.list = list;
            this.chatListLayout = chatListLayout;
        }

        @Override
        public int getCount() {
            return list.size();
        }

        @Override
        public Object getItem(int position) {
            return list.get(position);
        }

        @Override
        public long getItemId(int position) {
            return position;
        }

        @Override
        public View getView(int position, View convertView, ViewGroup parent) {
            View view = LayoutInflater.from(this.context).inflate(this.chatListLayout, parent, false);
            TextView textView = (TextView) view.findViewById(R.id.item_text);
            textView.setText(list.get(position));

            return view;
        }
    }
