# Android memo
## Basic usage
- **Text element**

><pre>TextView myTextView = (TextView) findViewById(R.id.myTextView);</pre>

- **Set text**

><pre>myTextView.setText("My string");</pre>

- **Button element** (exist also as ImageButton)

><pre>Button myButton = (Button) findViewById(R.id.myButton);</pre>

- **Image element**

><pre>ImageView myImage = (ImageView)findViewById(R.id.myImage);</pre>

- **Set image**

><pre>myImage.setImageResource(R.drawable.myImageId);</pre>

- **Array**

><pre>int[] myImgArray = {R.drawable.img1,
                R.drawable.img2,
                R.drawable.img3,
                R.drawable.img4,
                R.drawable.img5,
                R.drawable.img6
 };
</pre>

- **Event listener**
    - Anonymous listener version :
><pre>myElement.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //listener content
            }
        });
</pre>

    - or named version :
><pre>View.OnClickListener myListener = new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            //listener content
        }
    };
myElement.setOnClickListener(myListener);
</pre>

- **Toast**
><pre>Toast.makeText(getApplicationContext(), "myText", Toast.LENGTH_SHORT).show();
</pre>

- **Random generator**
><pre>Random randomNumberGenerator = new Random();
 int number = randomNumberGenerator.nextInt(6); // will get a random number between 0 and 5
</pre>

- **SaveInstanceState**
    - *Get data onCreate*

    ><pre>if (savedInstanceState != null) {
            mSomeIndex = savedInstanceState.getInt(KEY_INDEX, 0);
            mSomeString = savedInstanceState.getString(SOME_STRING, '');
            mSomeBoolean = savedInstanceState.getBoolean(SOME_BOOL, false);
    }</pre>

    - *Put data inside savedInstanceState*
    ><pre>@Override
    public void onSaveInstanceState(Bundle savedInstanceState) {
            super.onSaveInstanceState(savedInstanceState);
            Log.d(TAG, "onSaveInstanceState");
            savedInstanceState.putInt(KEY_INDEX, mCurrentIndex);
            savedInstanceState.putBoolean(USER_CHEATED, mIsCheater);
    }</pre>


- **Intent**
    - **Create a new intent**

    ><pre>Intent intent = new Intent(MyActivity.this, NewActivity.class);
StartActivity(intent);</pre>

    - **Pass data using intent and extras**
    ><pre>intent.putExtra(MY_EXTRA, someBoolean);
intent.putExtra(MY_EXTRA, someString);</pre>

    - **Use newIntent to encapsulate what the new activity needs (this code goes inside the new activity, at the top)**
    ><pre>public static Intent newIntent(Context packageContext, boolean someBoolean) {
            Intent intent = new Intent(packageContext, newActivity.class);
            intent.putExtra(MY_EXTRA, someBoolean);
            return intent;
    }</pre>

    - **Now use this method to actually launch this newIntent**
    ><pre>Intent intent = NewActivity.newIntent(MyActivity.this, someBoolean);
    startActivity(intent);</pre>

    - *Retrieve data inside the intent (extra)*
    ><pre>myBool = getIntent().getBooleanExtra(EXTRA_BOOL, false);</pre>

    - **Prepare to get some results back from the child activity (startActivityForResult)**
    ><pre>startActivityForResult(intent, SOME_FINAL_INT); // instead of startActivity(intent);</pre>

    - **Set result in the child activity**
    ><pre>private void setSomething(boolean someBoolean) {
            Intent data = new Intent();
            data.putExtra(SOME_EXTRA, someBoolean);
            setResult(RESULT_OK, data);
    }</pre>

    - **Get the results back from the child activity**
    ><pre>@Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            //insert some logic here using the resultCode
    }</pre>

- **Create a random ID**
><pre>private UUID mId;
mId = UUID.randomUUID();
</pre>

- **Singleton**
  - A singleton is a class that allows only one instance of itself to be created.
  It contains a private constructor and a get() method. If the instance already exists, then get() return this instance. If it doesn't exist, then get() will call the constructor.
  <pre>public class mySingleton {
      private static mySingleton sMySingleton;
      public static mySingleton get (Context context){
          if (sMySingleton == null) {
              sMySingleton = new mySingleton(context);
          }
          return sMySingleton;
      }
      private mySingleton(Context context) {

      }
}</pre>

## Fragments

- A fragment is a controller object, it interacts with the model and updates the view accordingly
- It got a similar lifecycle as activities that needs to be implemented
- A UI fragment has a view of its own inflated from a layout file
- The activity contains a spot where the fragment will be inserted (can be multiple spots for multiple fragments)
- *Somehow it looks like a kind of include inside the activity and the layout by extension*
- **Use support fragments *(using android appcompat dependency)***

- ### Create a UI Fragment:
    - Create the UI by defining widgets in a layout file (like for an activity)
    - Create the class and set its view (pointing to the layout we just created)

        ><pre>public class CrimeFragment extends Fragment {
                @Override
                public void onCreate(Bundle savedInstanceState) {
                    super.onCreate(savedInstanceState);
                }
                @Override
                public View onCreateView(
                        LayoutInflater inflater,
                        ViewGroup container,
                        Bundle savedInstanceState) {
                    super.onCreateView(inflater, container, savedInstanceState);
                View v = inflater.inflate(R.layout.my_fragment, container, false);
                return v;
        }</pre>

        - We do not inflate the view inside onCreate. Instead we configure the fragment's instance inside onCreate and create the fragment's view inside onCreateView. onCreateView inflate the layout for the fragment's view, and return the inflated View to the hosting activity.
        - We pass 3 parameters to LayoutInflater.inflate:
            - the resource ID of the fragment layout
            - the parent's view
            - the 3rd parameter tells the inflater wether to add the inflated view to its view's parent. (usually pass false because we add the view in the activity code)

    - Wire up the widgets inflated from the layout in code
        - It works nearly the same as in Activity.onCreate (select widget, set event listeners ...)

        ><pre>myButton = (Button) v.findViewById(R.id.my_button);</pre>


  - ### Host a UI Fragment:
      - Can use a FrameLayout inside the main\_activity.xml to host the fragment (instead of default layout, the layout also need an ID)
      - We use a FragmentManager to manage the fragments (inside onCreate)
      ><pre>FragmentManager fm = getSupportFragmentManager();
      Fragment fragment = fm.findFragmentById(R.id.fragment_container);
      if (fragment == null) {
              fragment = createFragment();
              fm.beginTransaction()
                      .add(R.id.fragment_container, fragment)
                      .commit();
      }</pre>

        - add() creates and commits a *fragment transaction*
        - Fragment transaction are used to add, remove, attach, detach or replace fragments in the fragments list.
        - FragmentManager maintains a back stack of fragment transactions.
        - add has 2 parameters: a container view ID and the new Fragment.
        - When we need to retrieve the fragment from the FragmentManager, we ask for it by using the container view ID:  *fm.findFragmentById(R.id.fragment_container);*

## RecyclerView
- RecyclerView role is to recycle TextViews and position them on screen
- May need to add recyclerview-v7 library to the dependencies
- Require a RecyclerView layout in the xml layout file
- RecyclerView does not position items on the screen itself, it delegates the job to the LayoutManager.
<pre>
@Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstancesState) {
        View view = inflater.inflate(R.layout.fragment_my_list, container, false);

        mMyRecyclerView = (RecyclerView) view.findViewById(R.id.my_recycler_view);
        mMyRecyclerView.setLayoutManager(new LinearLayoutManager(getActivity()));

        return view;
    }
</pre>

- We pull the views we want to update inside the constructor like so:
<pre>
private class CrimeHolder extends RecyclerView.ViewHolder {
    private TextView mTitleTextView;
    private ImageView mSolvedImageView;
    public CrimeHolder (LayoutInflater inflater, ViewGroup parent) {
        super(inflater.inflate(R.layout.list_item_crime, parent, false));
        mTitleTextView = (TextView) itemView.findViewById(R.id.crime_title);
        mSolvedImageView = (ImageView) itemView.findViewById(R.id.crime_solved);
    }
</pre>
- We bind the data inside the bind method
<pre>
public void bind(Crime crime) {
    mCrime = crime;
    mTitleTextView.setText(mCrime.getTitle());
    mSolvedImageView.setVisibility(crime.isSolved() ? View.VISIBLE : View.GONE);
}
</pre>

- ### ViewHolder
  - His job is to hold on to a View
  <pre>
  private class ListRow extends RecyclerView.ViewHolder{
        private TextView mTitleTextView;
        public ListRow (View view) {
            super(view);
            mTitleTextView = (TextView) view.findViewById(R.id.myTitle);
        }
    }
  </pre>
  -  A typical usage of ViewHolder
  <pre>
    ListRow row = new ListRow(inflater.inflate(R.layout.list_row, parent, false));
    View view;
    TextView titleView =row.mTitleTextView;
    }
  </pre>
  - Another example
  <pre>
  private class CrimeHolder extends RecyclerView.ViewHolder{
      public CrimeHolder (LayoutInflater inflater, ViewGroup parent) {
          super(inflater.inflate(R.layout.list_item_crime, parent, false));

          //Code to manipulate the view
      }
  }
  </pre>
  - ViewHolder can handle click event too, but we need to implement the View.OnClickListener interface, then add itemView.setOnClickListener(this) inside the ViewHolder. Finally override the onClick method to add the logic we want.
  <pre>
  private class CrimeHolder extends RecyclerView.ViewHolder
            implements View.OnClickListener {
      ...
      public CrimeHolder (LayoutInflater inflater, ViewGroup parent) {
            super(inflater.inflate(R.layout.list_item_crime, parent, false));
            itemView.setOnClickListener(this);
            ...
        }
        ...
        @Override
        public void onClick(View view) {
            Toast.makeText(getActivity(), mCrime.getTitle() + "clicked!", Toast.LENGTH_SHORT).show();
        }
  </pre>

- ### Adapter
    - His job is to create the necessary ViewHolders and bind ViewHolders to data from the model layer
    - The adapter wrap the data (can be a list)
    - Communication example between the Adapter and the RecylerView:
      - RecylerView -----getItemCount()-----&gt; Adapter
      - RecylerView &lt;-----100----- Adapter
      - RecylerView -----onCreateViewHolder(ViewGroup, int)-----&gt; Adapter
      - RecylerView &lt;-----ViewHolder----- Adapter
      - RecylerView -----onBindViewHolder(ViewGroup,0)-----&gt; Adapter
      - RecylerView -----onCreateViewHolder(...)-----> Adapter
      - RecylerView &lt;-----ViewHolder----- Adapter
      - RecylerView -----onBindViewHolder(ViewGroup,1)-----&gt; Adapter
    - Example of an adapter:
      <pre>
        private class CrimeAdapter extends RecyclerView.Adapter&lt;CrimeHolder&gt; {      
          private List&lt;Crime&gt; mCrimes;
          public CrimeAdapter(List&lt;Crime&gt; crimes) {
              mCrimes = crimes;
          }
          @Override
          public CrimeHolder onCreateViewHolder(ViewGroup parent, int viewType) {
              LayoutInflater layoutInflater = LayoutInflater.from(getActivity());

              return new CrimeHolder(layoutInflater, parent);
          }
          @Override
          public void onBindViewHolder(CrimeHolder holder, int position){
              Crime crime = mCrimes.get(position);
              holder.bind(crime);
          }
          @Override
          public int getItemCount() {
              return mCrimes.size();
          }
      }
    </pre>
    - We connect the Adapter to the RecyclerView inside onCreateViewHolder (we can update the UI there using a custom method)
      <pre>
      private void updateUI() {
        CrimeLab crimeLab = CrimeLab.get(getActivity());
        List&lt;Crime&gt; crimes = crimeLab.getCrimes();
        mAdapter = new CrimeAdapter(crimes);
        mCrimeRecyclerView.setAdapter(mAdapter);
      }
      </pre>

    - We bind the data to the actual viewholder using the onBindViewHolder method of the Adapter
      <pre>
      @Override
      public void onBindViewHolder(CrimeHolder holder, int position){
          Crime crime = mCrimes.get(position);
          holder.bind(crime);
      }
      </pre>




- **new**
><pre>

</pre>
