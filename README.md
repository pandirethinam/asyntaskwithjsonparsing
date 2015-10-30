# asyntaskwithjsonparsing
jsonparsing
public class MainActivity extends Activity {
    private static String url = "http://api.androidhive.info/contacts/";
    String response;
    ProgressDialog pDialog;
    ListView list;
  //  ArrayList<String> itemList;
   // HashMap<String,String> contactMap;
   // ArrayList<HashMap<String,String>> allValues=new ArrayList<>();
    ContactBean contactItemBeanObject;
    ArrayList<ContactBean> contactListBeanValues=new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        list= (ListView) findViewById(R.id.contactList);
      //  itemList=new ArrayList<>();
        new DownloadDataFromServer().execute();

    }

    public class DownloadDataFromServer extends AsyncTask<String,Void,String>{

        @Override
        protected String doInBackground(String... params) {
            try {
                // http client
                DefaultHttpClient httpClient = new DefaultHttpClient();
                HttpGet httpGet = new HttpGet(url);
                HttpResponse httpResponse = httpClient.execute(httpGet);
                HttpEntity httpEntity = httpResponse.getEntity();
                response = EntityUtils.toString(httpEntity);
            } catch (Exception e) {
                e.printStackTrace();
            }

            return response;
        }

        @Override
        protected void onPostExecute(String s) {
            super.onPostExecute(s);
            if(pDialog.isShowing()){
                pDialog.dismiss();
            }
            try {
                Log.e("Response", s);
                JSONObject resObj = new JSONObject(s);
                JSONArray arrayObj = resObj.getJSONArray("contacts");



                for (int i = 0; i < arrayObj.length(); i++) {

                    JSONObject itemObj = arrayObj.getJSONObject(i);
                    JSONObject itemobj1 = itemObj.getJSONObject("phone");




                        contactItemBeanObject = new ContactBean();
                        contactItemBeanObject.setName(itemObj.getString("name"));
                        contactItemBeanObject.setEmail(itemObj.getString("email"));
                        contactItemBeanObject.setGender(itemObj.getString("gender"));
                        contactItemBeanObject.setMobile(itemobj1.getString("mobile"));
                        contactItemBeanObject.setHome(itemobj1.getString("home"));
                        contactItemBeanObject.setOffice(itemobj1.getString("office"));



                   /* contactMap=new HashMap<>();
                    contactMap.put("Name",itemObj.getString("name"));
                    contactMap.put("Email",itemObj.getString("email"));
                    contactMap.put("Gender", itemObj.getString("gender"));
                    allValues.add(contactMap);*/

                        contactListBeanValues.add(contactItemBeanObject);

                         Log.e("Name" + i, itemObj.getString("name"));
                    }
                     // itemList.add(itemObj.getString("email"));

            ContactAdapter adapter=new ContactAdapter(MainActivity.this,R.layout.list_item,contactListBeanValues);
                list.setAdapter(adapter);
                list.setOnItemClickListener(new AdapterView.OnItemClickListener() {
                    @Override
                    public void onItemClick(AdapterView<?> parent, View view, int item, long id) {
                        ContactBean cb=contactListBeanValues.get(item);
                        Toast.makeText(getApplicationContext(),cb.getEmail(),Toast.LENGTH_SHORT).show();
                    }
                });




            }catch (Exception e){

            }

        }

        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            pDialog=new ProgressDialog(MainActivity.this);
            pDialog.setMessage("Loading Please wait...");
            pDialog.setCancelable(false);
            pDialog.show();

        }
    }


}
