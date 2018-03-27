###Android入门1_传递数据的几种方式

1. 通过Intent的Intent.Extra方法传递数据。

首先应该明白什么是Intent意图，android中的每个应用都是运行在一个单独的虚拟机中，一个应用的activity想打开本应用活着其他应用的activity就需要使用intent传递一个指令，从而打开activity。

我们想实现，在一个activity中点击一个按钮，将输入好的值显示在另个activity中。

新建一个工程，在MainActivity填写：
  package com.shiyang.android_intent1;  

  import android.content.Intent;  
  import android.support.v7.app.AppCompatActivity;  
  import android.os.Bundle;  
  import android.view.View;  
  import android.widget.Button;  

  public class MainActivity extends AppCompatActivity {  

      private Button button;  

      @Override  
      protected void onCreate(Bundle savedInstanceState) {  
          super.onCreate(savedInstanceState);  
          setContentView(R.layout.activity_main);  
          button = (Button)findViewById(R.id.button);  
          button.setOnClickListener(new View.OnClickListener() {  
              @Override  
              public void onClick(View view) {  
                  Intent intent = new Intent(MainActivity.this, OtherActivity.class);  
                  intent.putExtra("name","Jack");//存放键值对   
                  intent.putExtra("age",23);  
                  intent.putExtra("address","sanya");  
                  startActivity(intent);  
              }  
          });  
      }  
  }  
