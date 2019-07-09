---
layout: post
title:  "Android Volley"
date:   2019-07-09 13:40:00 +0700
categories: mobile
---

<h2>1. Buat project baru, tambahkan line ini di gradle App</h2>
{% highlight java %}

implementation 'com.android.volley:volley:1.1.1'

{% endhighlight %}

<h2>2. Tambahkan internet permisssion pada Android Manifest</h2>
{% highlight xml %}

<manifest xmlns:android="http://schemas.android.com/apk/res/android"
package="com.example.volleygithub">

<uses-permission android:name="android.permission.INTERNET" />

<application
android:allowBackup="true"
android:icon="@mipmap/ic_launcher"
android:label="@string/app_name"
android:roundIcon="@mipmap/ic_launcher_round"
android:supportsRtl="true"
android:theme="@style/AppTheme">

<activity android:name=".MainActivity" >
<intent-filter>
<action android:name="android.intent.action.MAIN" />

<category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
</activity>

</application>
</manifest> 

{% endhighlight %}

<h2>3. Buat file .java baru dengan nama AppSingleton, isi dengan</h2>
{% highlight java %}



import android.content.Context;
import android.graphics.Bitmap;
import android.util.LruCache;

import com.android.volley.Request;
import com.android.volley.RequestQueue;
import com.android.volley.toolbox.ImageLoader;
import com.android.volley.toolbox.Volley;

/**
    * Created by androidtutorialpoint on 5/11/16.
    */
public class AppSingleton {
    private static AppSingleton mAppSingletonInstance;
    private RequestQueue mRequestQueue;
    private ImageLoader mImageLoader;
    private static Context mContext;
    private AppSingleton(Context context) {
        mContext = context;
        mRequestQueue = getRequestQueue();
        mImageLoader = new ImageLoader(mRequestQueue,
                new ImageLoader.ImageCache() {
                    private final LruCache<String, Bitmap>
                            cache = new LruCache<String, Bitmap>(20);

                    @Override
                    public Bitmap getBitmap(String url) {
                        return cache.get(url);
                    }

                    @Override
                    public void putBitmap(String url, Bitmap bitmap) {
                        cache.put(url, bitmap);
                    }
                });
    }

    public static synchronized AppSingleton getInstance(Context context) {
        if (mAppSingletonInstance == null) {
            mAppSingletonInstance = new AppSingleton(context);
        }
        return mAppSingletonInstance;
    }

    public RequestQueue getRequestQueue() {
        if (mRequestQueue == null) {
            // getApplicationContext() is key, it keeps you from leaking the
            // Activity or BroadcastReceiver if someone passes one in.
            mRequestQueue = Volley.newRequestQueue(mContext.getApplicationContext());
        }
        return mRequestQueue;
    }

    public <T> void addToRequestQueue(Request<T> req,String tag) {
        req.setTag(tag);
        getRequestQueue().add(req);
    }

    public ImageLoader getImageLoader() {
        return mImageLoader;
    }

    public void cancelPendingRequests(Object tag) {
        if (mRequestQueue != null) {
            mRequestQueue.cancelAll(tag);
        }
    }
}

{% endhighlight %}

<h2>4. Buat .java dengan nama MainActivity, isi dengan : </h2>
{% highlight java %}



import android.app.ProgressDialog;

import android.content.DialogInterface;
import android.support.v7.app.AlertDialog;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.Button;
import android.widget.ImageView;
import android.widget.TextView;

import com.android.volley.Cache;
import com.android.volley.Request;
import com.android.volley.Response;
import com.android.volley.VolleyError;
import com.android.volley.VolleyLog;
import com.android.volley.toolbox.ImageLoader;
import com.android.volley.toolbox.JsonArrayRequest;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.StringRequest;
import org.json.JSONArray;
import org.json.JSONObject;
import java.io.UnsupportedEncodingException;

public class MainActivity extends AppCompatActivity {

//    private static final String IMAGE_REQUEST_URL = "https://androidtutorialpoint.com/api/lg_nexus_5x";
//    private static final String STRING_REQUEST_URL = "https://androidtutorialpoint.com/api/volleyString";
//    private static final String JSON_OBJECT_REQUEST_URL = "https://androidtutorialpoint.com/api/volleyJsonObject";
//    private static final String JSON_ARRAY_REQUEST_URL = "https://androidtutorialpoint.com/api/volleyJsonArray";

    private static final String IMAGE_REQUEST_URL = "https://l200160140.github.io/android/api/image/arya.jpg";
    private static final String STRING_REQUEST_URL = "https://l200160140.github.io/android/api/string";
    private static final String JSON_OBJECT_REQUEST_URL = "https://l200160140.github.io/android/api/jsonObject";
    private static final String JSON_ARRAY_REQUEST_URL = "https://l200160140.github.io/android/api/jsonarray";
//    private static final String JSON_ARRAY_REQUEST_URL = "https://bana-handaga.github.io/android/api/jsonarray/";

    ProgressDialog progressDialog;
    private static final String TAG = "MainActivity";
    private Button stringRequestButton;
    private Button JsonObjectRequestButton;
    private Button JsonArrayRequestButton;
    private Button ImageRequestButton;
    private View showDialogView;
    private TextView outputTextView;
    private ImageView outputImageView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        progressDialog = new ProgressDialog(this);

        stringRequestButton = (Button)findViewById(R.id.button_get_string);
        JsonObjectRequestButton = (Button)findViewById(R.id.button_get_Json_object);
        JsonArrayRequestButton = (Button)findViewById(R.id.button_get_Json_array);
        ImageRequestButton = (Button)findViewById(R.id.button_get_image);

        stringRequestButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                volleyStringRequst(STRING_REQUEST_URL);
            }
        });
        JsonObjectRequestButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                volleyJsonObjectRequest(JSON_OBJECT_REQUEST_URL);
            }
        });
        JsonArrayRequestButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                volleyJsonArrayRequest(JSON_ARRAY_REQUEST_URL);
            }
        });
        ImageRequestButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                volleyImageLoader(IMAGE_REQUEST_URL);
            }
        });

    }


    public void volleyStringRequst(String url){

        String  REQUEST_TAG = "com.androidtutorialpoint.volleyStringRequest";
        progressDialog.setMessage("Loading...");
        progressDialog.show();

        StringRequest strReq = new StringRequest(url, new Response.Listener<String>() {
            @Override
            public void onResponse(String response) {
                Log.d(TAG, response.toString());

                LayoutInflater li = LayoutInflater.from(MainActivity.this);
                showDialogView = li.inflate(R.layout.show_dialog, null);
                outputTextView = (TextView)showDialogView.findViewById(R.id.text_view_dialog);
                AlertDialog.Builder alertDialogBuilder = new AlertDialog.Builder(MainActivity.this);
                alertDialogBuilder.setView(showDialogView);
                alertDialogBuilder
                        .setPositiveButton("OK", new DialogInterface.OnClickListener() {
                            public void onClick(DialogInterface dialog, int id) {
                            }
                        })
                        .setCancelable(false)
                        .create();
                outputTextView.setText(response.toString());
                alertDialogBuilder.show();
                progressDialog.hide();
            }
        }, new Response.ErrorListener() {

            @Override
            public void onErrorResponse(VolleyError error) {
                VolleyLog.d(TAG, "Error: " + error.getMessage());
                progressDialog.hide();
            }
        });
        // Adding String request to request queue
        AppSingleton.getInstance(getApplicationContext()).addToRequestQueue(strReq, REQUEST_TAG);
    }

    public void volleyJsonObjectRequest(String url){

        String  REQUEST_TAG = "com.androidtutorialpoint.volleyJsonObjectRequest";
        progressDialog.setMessage("Loading...");
        progressDialog.show();

        JsonObjectRequest jsonObjectReq = new JsonObjectRequest(url, null,
                new Response.Listener<JSONObject>() {
                    @Override
                    public void onResponse(JSONObject response) {
                        Log.d(TAG, response.toString());

                        LayoutInflater li = LayoutInflater.from(MainActivity.this);
                        showDialogView = li.inflate(R.layout.show_dialog, null);
                        outputTextView = (TextView)showDialogView.findViewById(R.id.text_view_dialog);
                        AlertDialog.Builder alertDialogBuilder = new AlertDialog.Builder(MainActivity.this);
                        alertDialogBuilder.setView(showDialogView);
                        alertDialogBuilder
                                .setPositiveButton("OK", new DialogInterface.OnClickListener() {
                                    public void onClick(DialogInterface dialog, int id) {
                                    }
                                })
                                .setCancelable(false)
                                .create();
                        outputTextView.setText(response.toString());
                        alertDialogBuilder.show();
                        progressDialog.hide();

                    }
                }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                VolleyLog.d(TAG, "Error: " + error.getMessage());
                progressDialog.hide();
            }
        });

        // Adding JsonObject request to request queue
        AppSingleton.getInstance(getApplicationContext()).addToRequestQueue(jsonObjectReq,REQUEST_TAG);
    }
    public void volleyJsonArrayRequest(String url){

        String  REQUEST_TAG = "com.androidtutorialpoint.volleyJsonArrayRequest";
        progressDialog.setMessage("Loading...");
        progressDialog.show();

        JsonArrayRequest jsonArrayReq = new JsonArrayRequest(url,
                new Response.Listener<JSONArray>() {
                    @Override
                    public void onResponse(JSONArray response) {
                        Log.d(TAG, response.toString());
                        LayoutInflater li = LayoutInflater.from(MainActivity.this);
                        showDialogView = li.inflate(R.layout.show_dialog, null);
                        outputTextView = (TextView)showDialogView.findViewById(R.id.text_view_dialog);
                        AlertDialog.Builder alertDialogBuilder = new AlertDialog.Builder(MainActivity.this);
                        alertDialogBuilder.setView(showDialogView);
                        alertDialogBuilder
                                .setPositiveButton("OK", new DialogInterface.OnClickListener() {
                                    public void onClick(DialogInterface dialog, int id) {
                                    }
                                })
                                .setCancelable(false)
                                .create();
                        outputTextView.setText(response.toString());
                        alertDialogBuilder.show();
                        progressDialog.hide();                    }
                }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                VolleyLog.d(TAG, "Error: " + error.getMessage());
                progressDialog.hide();
            }
        });

        // Adding JsonObject request to request queue
        AppSingleton.getInstance(getApplicationContext()).addToRequestQueue(jsonArrayReq, REQUEST_TAG);
    }

    public void volleyImageLoader(String url){
        ImageLoader imageLoader = AppSingleton.getInstance(getApplicationContext()).getImageLoader();

        imageLoader.get(url, new ImageLoader.ImageListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                Log.e(TAG, "Image Load Error: " + error.getMessage());
            }

            @Override
            public void onResponse(ImageLoader.ImageContainer response, boolean arg1) {
                if (response.getBitmap() != null) {

                    LayoutInflater li = LayoutInflater.from(MainActivity.this);
                    showDialogView = li.inflate(R.layout.show_dialog, null);
                    outputImageView = (ImageView)showDialogView.findViewById(R.id.image_view_dialog);
                    AlertDialog.Builder alertDialogBuilder = new AlertDialog.Builder(MainActivity.this);
                    alertDialogBuilder.setView(showDialogView);
                    alertDialogBuilder
                            .setPositiveButton("OK", new DialogInterface.OnClickListener() {
                                public void onClick(DialogInterface dialog, int id) {
                                }
                            })
                            .setCancelable(false)
                            .create();
                    outputImageView.setImageBitmap(response.getBitmap());
                    alertDialogBuilder.show();
                }
            }
        });
    }

    public void volleyCacheRequest(String url){
        Cache cache = AppSingleton.getInstance(getApplicationContext()).getRequestQueue().getCache();
        Cache.Entry entry = cache.get(url);
        if(entry != null){
            try {
                String data = new String(entry.data, "UTF-8");
                // handle data, like converting it to xml, json, bitmap etc.,
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
        }
        else{

        }
    }

    public void volleyInvalidateCache(String url){
        AppSingleton.getInstance(getApplicationContext()).getRequestQueue().getCache().invalidate(url, true);
    }

    public void volleyDeleteCache(String url){
        AppSingleton.getInstance(getApplicationContext()).getRequestQueue().getCache().remove(url);
    }

    public void volleyClearCache(){
        AppSingleton.getInstance(getApplicationContext()).getRequestQueue().getCache().clear();
    }

}

{% endhighlight %}

<h2></h2>

{% highlight xml %}

  <?xml version="1.0" encoding="utf-8"?> 
 <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" 
    xmlns:tools="http://schemas.android.com/tools" 
    android:layout_width="match_parent" 
    android:layout_height="match_parent" 
    android:orientation="vertical" 
    android:paddingLeft="16dp" 
    android:paddingRight="@dimen/activity_horizontal_margin" 
    android:paddingTop="@dimen/activity_vertical_margin" 
    android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
     <TextView 
        android:layout_width="wrap_content" 
        android:layout_height="wrap_content" 
        android:text="Welcome to Android Volley Tutorial !!\n Choose a menu option." 
        android:layout_marginTop="20dp" 
        android:textAlignment="center" 
        style="@android:style/TextAppearance.DeviceDefault.Widget.ActionBar.Menu" 
        android:textSize="15sp" 
        android:layout_gravity="center"/> 
     <Button 
        android:id="@+id/button_get_string" 
        android:layout_marginTop="5dp" 
        android:layout_marginBottom="5dp" 
        android:layout_width="match_parent" 
        android:layout_height="50dp" 
        android:text="Get String "/> 
     <Button 
        android:id="@+id/button_get_Json_object" 
        android:layout_marginTop="5dp" 
        android:layout_marginBottom="5dp" 
        android:layout_width="match_parent" 
        android:layout_height="50dp" 
        android:text="Get JsonObject "/> 
     <Button 
        android:id="@+id/button_get_Json_array" 
        android:layout_marginTop="5dp" 
        android:layout_marginBottom="5dp" 
        android:layout_width="match_parent" 
        android:layout_height="50dp" 
        android:text="Get JsonArray "/> 
     <Button 
        android:id="@+id/button_get_image" 
        android:layout_marginTop="5dp" 
        android:layout_marginBottom="5dp" 
        android:layout_width="match_parent" 
        android:layout_height="50dp" 
        android:text="Get Image "/>
 </LinearLayout>

{% endhighlight %}

<h2>6. Pada res->layout, buat file show_dialog.xml , isi dengan : </h2>

{% highlight xml %}

 <?xml version="1.0" encoding="utf-8"?> 
 <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" 
    android:id="@+id/layout_root" 
    android:layout_width="fill_parent" 
    android:layout_height="fill_parent" 
    android:orientation="vertical" 
    android:padding="5dp" > 
    <TextView 
        android:id="@+id/text_view_dialog" 
        android:layout_width="wrap_content" 
        android:layout_height="wrap_content" 
        android:layout_gravity="center" 
        android:textAlignment="center" 
        android:gravity="center" 
        style="@android:style/TextAppearance.DeviceDefault.Widget.ActionBar.Menu" /> 
    <ImageView 
        android:id="@+id/image_view_dialog" 
        android:layout_width="match_parent" 
        android:layout_height="wrap_content"/> 
 </LinearLayout>

{% endhighlight %}

<h2>7. Pada res->values, buat file dimens.xml, isi dengan : </h2>

{% highlight xml %}

<resources> 
    <!-- Default screen margins, per the Android Design guidelines. --> 
    <dimen name="activity_horizontal_margin">16dp</dimen> 
    <dimen name="activity_vertical_margin">16dp</dimen> 
</resources>

{% endhighlight %}
<!-- <h3>Dokumentasi</h3> -->

<!-- <h2>Gambar</h2>
![Hasil-1](/assets/embedded/uas/hasil1.jpg){:class="img-responsive"}
<br><br>
![Hasil-2](/assets/embedded/uas/hasil2.jpg){:class="img-responsive"}

<h2>Video</h2>
<video width="320" height="240" controls>
  <source src="/assets/embedded/uas/hasil.mp4" type="video/mp4">
Your browser does not support the video tag.
</video> -->