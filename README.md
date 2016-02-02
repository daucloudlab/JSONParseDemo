# JSONParseDemo
JSON Parsing demo project by volley

Кэшті анықтаймыз:

import com.android.volley.toolbox.ImageLoader.ImageCache;
 
import android.graphics.Bitmap;
import android.support.v4.util.LruCache;
 
public class LruBitmapCache extends LruCache<String, Bitmap> implements
        ImageCache {
    public static int getDefaultLruCacheSize() {
        final int maxMemory = (int) (Runtime.getRuntime().maxMemory() / 1024);
        final int cacheSize = maxMemory / 8;
 
        return cacheSize;
    }
 
    public LruBitmapCache() {
        this(getDefaultLruCacheSize());
    }
 
    public LruBitmapCache(int sizeInKiloBytes) {
        super(sizeInKiloBytes);
    }
 
    @Override
    protected int sizeOf(String key, Bitmap value) {
        return value.getRowBytes() * value.getHeight() / 1024;
    }
 
    @Override
    public Bitmap getBitmap(String url) {
        return get(url);
    }
 
    @Override
    public void putBitmap(String url, Bitmap bitmap) {
        put(url, bitmap);
    }
}

AppController құрамыз. Application-нің мұрагері болуы керек

import info.androidhive.volleyexamples.volley.utils.LruBitmapCache;
import android.app.Application;
import android.text.TextUtils;
 
import com.android.volley.Request;
import com.android.volley.RequestQueue;
import com.android.volley.toolbox.ImageLoader;
import com.android.volley.toolbox.Volley;
 
public class AppController extends Application {
 
    public static final String TAG = AppController.class
            .getSimpleName();
 
    private RequestQueue mRequestQueue;
    private ImageLoader mImageLoader;
 
    private static AppController mInstance;
 
    @Override
    public void onCreate() {
        super.onCreate();
        mInstance = this;
    }
 
    public static synchronized AppController getInstance() {
        return mInstance;
    }
 
    public RequestQueue getRequestQueue() {
        if (mRequestQueue == null) {
            mRequestQueue = Volley.newRequestQueue(getApplicationContext());
        }
 
        return mRequestQueue;
    }
 
    public ImageLoader getImageLoader() {
        getRequestQueue();
        if (mImageLoader == null) {
            mImageLoader = new ImageLoader(this.mRequestQueue,
                    new LruBitmapCache());
        }
        return this.mImageLoader;
    }
 
    public <T> void addToRequestQueue(Request<T> req, String tag) {
        // set the default tag if tag is empty
        req.setTag(TextUtils.isEmpty(tag) ? TAG : tag);
        getRequestQueue().add(req);
    }
 
    public <T> void addToRequestQueue(Request<T> req) {
        req.setTag(TAG);
        getRequestQueue().add(req);
    }
 
    public void cancelPendingRequests(Object tag) {
        if (mRequestQueue != null) {
            mRequestQueue.cancelAll(tag);
        }
    }
}

AndroidManifest.xml-де көрсетеміз

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="info.androidhive.volleyexamples"
    android:versionCode="1"
    android:versionName="1.0" >
 
    <uses-sdk
        android:minSdkVersion="8"
        android:targetSdkVersion="19" />
 
    <uses-permission android:name="android.permission.INTERNET" />
 
    <application
        android:name="kz.kaznitu.apps.AppController"
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <!-- all activities and other stuff -->
    </application>
 
</manifest>

JSON object request

String tag_json_obj = "json_obj_req";
 
String url = "url address";
         
ProgressDialog pDialog = new ProgressDialog(this);
pDialog.setMessage("Loading...");
pDialog.show();     
         
        JsonObjectRequest jsonObjReq = new JsonObjectRequest(Method.GET,
                url, null,
                new Response.Listener<JSONObject>() {
 
                    @Override
                    public void onResponse(JSONObject response) {
                        Log.d(TAG, response.toString());
                        pDialog.hide();
                    }
                }, new Response.ErrorListener() {
 
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        VolleyLog.d(TAG, "Error: " + error.getMessage());
                        
                        pDialog.hide();
                    }
                });
 
AppController.getInstance().addToRequestQueue(jsonObjReq, tag_json_obj);

Json Array request

String tag_json_arry = "json_array_req";
 
String url = "url address";
         
ProgressDialog pDialog = new ProgressDialog(this);
pDialog.setMessage("Loading...");
pDialog.show();     
         
JsonArrayRequest req = new JsonArrayRequest(url,
                new Response.Listener<JSONArray>() {
                    @Override
                    public void onResponse(JSONArray response) {
                        Log.d(TAG, response.toString());        
                        pDialog.hide();             
                    }
                }, new Response.ErrorListener() {
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        VolleyLog.d(TAG, "Error: " + error.getMessage());
                        pDialog.hide();
                    }
                });
 
AppController.getInstance().addToRequestQueue(req, tag_json_arry);

String request

String  tag_string_req = "string_req";
 
String url = "url address";
         
ProgressDialog pDialog = new ProgressDialog(this);
pDialog.setMessage("Loading...");
pDialog.show();     
         
StringRequest strReq = new StringRequest(Method.GET,
                url, new Response.Listener<String>() {
 
                    @Override
                    public void onResponse(String response) {
                        Log.d(TAG, response.toString());
                        pDialog.hide();
 
                    }
                }, new Response.ErrorListener() {
 
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        VolleyLog.d(TAG, "Error: " + error.getMessage());
                        pDialog.hide();
                    }
                });
 
AppController.getInstance().addToRequestQueue(strReq, tag_string_req);

POST request with parameters

String tag_json_obj = "json_obj_req";
 
String url = "http://api.androidhive.info/volley/person_object.json";
         
ProgressDialog pDialog = new ProgressDialog(this);
pDialog.setMessage("Loading...");
pDialog.show();     
         
        JsonObjectRequest jsonObjReq = new JsonObjectRequest(Method.POST,
                url, null,
                new Response.Listener<JSONObject>() {
 
                    @Override
                    public void onResponse(JSONObject response) {
                        Log.d(TAG, response.toString());
                        pDialog.hide();
                    }
                }, new Response.ErrorListener() {
 
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        VolleyLog.d(TAG, "Error: " + error.getMessage());
                        pDialog.hide();
                    }
                }) {
 
            @Override
            protected Map<String, String> getParams() {
                Map<String, String> params = new HashMap<String, String>();
                params.put("name", "Androidhive");
                params.put("email", "abc@androidhive.info");
                params.put("password", "password123");
 
                return params;
            }
 
        };
 
AppController.getInstance().addToRequestQueue(jsonObjReq, tag_json_obj);

Кэшті басқару

Cache cache = AppController.getInstance().getRequestQueue().getCache();
Entry entry = cache.get(url);
if(entry != null){
    try {
        String data = new String(entry.data, "UTF-8");
        // handle data, like converting it to xml, json, bitmap etc.,
    } catch (UnsupportedEncodingException e) {      
        e.printStackTrace();
        }
    }
}else{
    // Cached response doesn't exists. Make network call here
}


AppController.getInstance().getRequestQueue().getCache().invalidate(url, true);
AppController.getInstance().getRequestQueue().getCache().remove(url);
AppController.getInstance().getRequestQueue().getCache().clear(url);
