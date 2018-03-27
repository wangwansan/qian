## Android入门1_传递数据的几种方式

### 1. 通过Intent的Intent.Extra方法传递数据。

首先应该明白什么是Intent意图，android中的每个应用都是运行在一个单独的虚拟机中，一个应用的activity想打开本应用活着其他应用的activity就需要使用intent传递一个指令，从而打开activity。

我们想实现，在一个activity中点击一个按钮，将输入好的值显示在另个activity中。

### 新建一个工程，在MainActivity填写：
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
### 新建一个activity为OtherActivity，填写代码：
    package com.shiyang.android_intent1;  

    import android.content.Intent;  
    import android.support.v7.app.AppCompatActivity;  
    import android.os.Bundle;  
    import android.util.Log;  
    import android.widget.TextView;  

    public class OtherActivity extends AppCompatActivity {  


        TextView textView ;  
        @Override  
        protected void onCreate(Bundle savedInstanceState) {  
            super.onCreate(savedInstanceState);  
            setContentView(R.layout.activity_other);  
            textView = (TextView)findViewById(R.id.Msg);  
            Intent intent = getIntent();  
            String name = intent.getStringExtra("name");  
            int age = intent.getIntExtra("age",0);  
            String address = intent.getStringExtra("address");  
            textView.setText("name is " + name + "age is "  +age+" address is " + address);  

        }  
    } 
## 2.主要学习利用剪贴板传送数据， 病学会使用android的ObjectOutputStream嵌套ByteArrayOutputStream。使用Base64完成字节数组和String 的相互转换.

### MainActivity:
    private Button button;  

        @Override  
        protected void onCreate(Bundle savedInstanceState) {  
            super.onCreate(savedInstanceState);  
            setContentView(R.layout.activity_main);  
            button = (Button) findViewById(R.id.button);  
            button.setOnClickListener(new View.OnClickListener() {  
                @Override  
                public void onClick(View view) {  
    //                ClipboardManager clipboardManager = (ClipboardManager) getSystemService(Context.CLIPBOARD_SERVICE);  
    //                String name = "jack";  
    //                ClipData clipData = ClipData.newPlainText("new Plain Text Lable",name);  
    //                clipboardManager.setPrimaryClip(clipData);  
    //                Intent intent = new Intent(MainActivity.this,OtherActivity.class);  
    //                startActivity(intent);  
                    MyData myData = new MyData("Jack",23);  
                    //将对象转换为字符串  
                    ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();  
                    String base64string = "";  
                    try {  
                        ObjectOutputStream objectOutputStream = new ObjectOutputStream(byteArrayOutputStream);  
                        objectOutputStream.writeObject(myData);  
                        base64string = Base64.encodeToString(byteArrayOutputStream.toByteArray(),Base64.DEFAULT);  
                        objectOutputStream.close();  
                    }catch (Exception e){  

                    }  
                    ClipboardManager clipboardManager = (ClipboardManager)getSystemService(Context.CLIPBOARD_SERVICE);  
                    ClipData clipData = ClipData.newPlainText("Label", base64string);  
                    clipboardManager.setPrimaryClip(clipData);  
                    Intent intent = new Intent(MainActivity.this,OtherActivity.class);  
                    startActivity(intent);  
                }  
            });  
        }  
### OtherActivity:
    private TextView textView;  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.other);  
        textView = (TextView)findViewById(R.id.msg);  
        ClipboardManager clipboardManager = (ClipboardManager)getSystemService(Context.CLIPBOARD_SERVICE);  
        ClipData clipData =  clipboardManager.getPrimaryClip();  
        String data = (String)clipboardManager.getPrimaryClip().getItemAt(0).getText();  

        byte[] base64 = Base64.decode(data,Base64.DEFAULT);  
        ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(base64);  
        try{  
            ObjectInputStream objectInputStream = new ObjectInputStream(byteArrayInputStream);  
            MyData myData = (MyData) objectInputStream.readObject();  
            textView.setText(myData.toString());  

        }catch (Exception e){  

        }  
    }  
 ### MyData:注意要继承序列化接口
     public class MyData implements Serializable {  

        public MyData(String name, int age) {  
            this.name = name;  
            this.age = age;  
        }  

        public String getName() {  
            return name;  
        }  

        public void setName(String name) {  
            this.name = name;  
        }  

        public int getAge() {  
            return age;  
        }  

        public void setAge(int age) {  
            this.age = age;  
        }  

        private String name;  
        private int age;  


        @Override  
        public String toString() {  
            return "Mydata [name =" + name +",age = " + age +"]";  
        }  
    }  

## 3. 利用Intent的startActivityForResult方法传递数据。这里做个例子，一个activity的数据发送到另一个，另一个再返回回来。

  ### MainActivity：
  
    public class MainActivity extends AppCompatActivity {  
      private final static int REQUEST_CODE = 1;  
      private EditText oneEditText;  
      private EditText twoEditText;  
      private EditText resultEditText;  

      private Button button;  


      private int oneNum;  
      private int twoNum;  


      @Override  
      protected void onCreate(Bundle savedInstanceState) {  
          super.onCreate(savedInstanceState);  
          setContentView(R.layout.activity_main);  

          oneEditText = (EditText)findViewById(R.id.oneAddEditText);  
          twoEditText = (EditText)findViewById(R.id.twoAddEditText);  
          resultEditText = (EditText)findViewById(R.id.resultEditText);  
          button = (Button)findViewById(R.id.button2);  

          button.setOnClickListener(new View.OnClickListener() {  
              @Override  
              public void onClick(View view) {  
                  oneNum = Integer.parseInt(oneEditText.getText().toString());  
                  twoNum = Integer.parseInt(twoEditText.getText().toString());  
                  Intent intent = new Intent(MainActivity.this,OtherActivity.class);  
                  intent.putExtra("One",oneNum);  
                  intent.putExtra("One",twoNum);  
                  startActivityForResult(intent,REQUEST_CODE);  

              }  
          });  
      }  

      @Override  
      protected void onActivityResult(int requestCode, int resultCode, Intent data) {  
          super.onActivityResult(requestCode, resultCode, data);  
          if(resultCode == 2){  
              if(requestCode == REQUEST_CODE){  
                  int three = data.getIntExtra("Three",0);  
                  resultEditText.setText(String.valueOf(three));  
              }  
          }  
      }  
  }  
  
### OtherActivity：
     public class OtherActivity extends AppCompatActivity {  

        private EditText resultEditText;  
        private Button returnMainBtn;  

        private final static int resultCode = 2;  
        @Override  
        protected void onCreate(Bundle savedInstanceState) {  
            super.onCreate(savedInstanceState);  
            setContentView(R.layout.activity_other);  
            resultEditText = (EditText)this.findViewById(R.id.ResultEditText2);  
            returnMainBtn = (Button)this.findViewById(R.id.returnMainBtn);  
            Intent intent = getIntent();  
            int one = intent.getIntExtra("One",0);  
            int two = intent.getIntExtra("Two",0);  
            int result;  
            result = one+two;  
            //resultEditText.setText(result);  
            //resultEditText.setText(result);  
            returnMainBtn.setOnClickListener(new View.OnClickListener() {  
                @Override  
                public void onClick(View view) {  
                    int three = Integer.parseInt(resultEditText.getText().toString());  
                    Intent inte = new Intent();  
                    inte.putExtra("Three",three);  
                    setResult(resultCode,inte); // 通过intent返回意图结果，通过setResult  
                    finish();//结束当前activity  
                    //startActivityForResult(inte,resultCode);  
                }  
            });  

        }  
    } 
    

重点在于，MainActivity中的resultCode要和OtherActivity的setOnClickListener方法中的SetResult参数一致，这样返会MainActivity中就可以对应。
## 4. 使用静态变量：

///使用intent传递数据是官方推荐，
//但是intent不能传递不能序列化的对象
//我们可以使用静态变量来解决这样的问题

### MainActivity：
    public class MainActivity extends AppCompatActivity {  

        private Button button;  

        @Override  
        protected void onCreate(Bundle savedInstanceState) {  
            super.onCreate(savedInstanceState);  
            setContentView(R.layout.activity_main);  

            button = (Button)this.findViewById(R.id.button);  
            button.setOnClickListener(new View.OnClickListener() {  
                @Override  
                public void onClick(View view) {  
                    OtherActivity.name = "Jack";  
                    OtherActivity.age = 23;  
                    Intent intent = new Intent();  
                    intent.setClass(MainActivity.this,OtherActivity.class);  
                    startActivity(intent);  
                }  
            });  
        }  
  }  
  ### OtherActivity：
    public class OtherActivity extends AppCompatActivity {  

      public static String name;  
      public static int age;  

      public TextView textView;  

      @Override  
      protected void onCreate(Bundle savedInstanceState) {  
          super.onCreate(savedInstanceState);  
          setContentView(R.layout.activity_other);  
          textView = (TextView) this.findViewById(R.id.Text);  
          textView.setText("name is " + name + " Age is " + age);  
      }  
   }  
