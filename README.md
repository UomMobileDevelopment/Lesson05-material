# Lesson05-material

2017 - 3/ Nov

Στο 5ο μάθημα θα προσθέσουμε μενού επιλογών έτσι ώστε να μπορεί ο χρήστης να αλλάξει πόλη πρόβλεψης. 

Ας δούμε ένα παράδειγμα οθόνης ρυθμίσεων

![Pref screen](https://github.com/UomMobileDevelopment/Lesson05-material/blob/master/preferences.png)

Οι ρυθμίσεις μπορούν να χωριστούν σε κατηγορίες με την προσθήκη Headers. Επίσης κάθε ρύθμιση εκτός του ονοματός της, έχει απο κάτω και το preference summary το οποίο είναι η τιμή που έχει δώθεί στη συγκεκριμένη ρύθμιση. 
Οι πιο γνωστοί τύποι ρυθμίσεων είναι:

- CheckBoxPreference
- ListPreference
- EditTextPreference

Οι τιμές των ρυθμίσεων αποθηκεύονται σε ένα αρχείο ```SharedPreferences``` με τη μορφή key-value


Συνεχίζουμε προσθέτοντας μια νέα κλάση με όνομα ```SettingsActivity```. Τον κώδικα για την κλάση μπορείτε να τον πάρετε απο εδώ: 
https://gist.github.com/udacityandroid/41aca2eb9ff6942e769b

παρατηρήστε πως άλλαξε το Addroid Manifest αρχείο για να προστεθεί το νέο Settings Activity.

Η επιλογή για εμφάνιση του ``SettingsActivity`` πρέπει να υπάρχει στο μενού τόσο του ``MainActivity`` όσο και του ``DetailActivity``
για να γίνει αυτό, τροποποιούμε (προσθέτουμε αν δεν υπάρχει) τη μέθοδο ``onOptionsItemSelected`` στις κλάσεις  ``MainActivity``  και  ``DetailActivity`` ως εξής:

```
@Override
    public boolean onOptionsItemSelected(MenuItem item) {
        // Handle action bar item clicks here. The action bar will
        // automatically handle clicks on the Home/Up button, so long
        // as you specify a parent activity in AndroidManifest.xml.
        int id = item.getItemId();

        if (id == R.id.action_settings) {
            Intent intent = new Intent(this, SettingsActivity.class);
            startActivity(intent);
            return true;
        }
        return super.onOptionsItemSelected(item);
    }
```

Αυτή τη στιγμή η εφαρμογή έχει οθόνη ρυθμίσεων αλλά είναι κενή! Πρέπει να προσθέσουμε ρυθμίσεις για τον χρήστη!

1. Δημιουργούμε ένα νέο φάκελο ``xml`` μέσα στο ``res``. (/res/xml). Δεξί κλικ στον res -> new -> Android Resource Directory
2. Δημιουργούμε ένα νέο αρχείο ``pref_general.xml`` μέσα στο folder ``xml``. Δεξί κλικ στον xml -> new -> XML Resource File. 
(Root Element = ```PreferenceScreen``` )
3. Προσθέτουμε ένα Location preference, στο οποίο ο χρήστης θα εισάγει τον κωδικό πόλης, της οποίας τον καιρό θέλει να βλέπει: 
```
 <EditTextPreference
        android:title="@string/pref_location_label"
        android:key="@string/pref_location_key"
        android:defaultValue="@string/pref_location_default"
        android:inputType="text"
        android:singleLine="true">
 </EditTextPreference>
```
Το πεδίο ``android:title`` είναι το κείμενο που θα φαίνεται στον χρήστη, π.χ. το **Display Name** ή το **Ringtone** στην παραπάνω εικόνα. 
Το πεδίο ``android:key`` είναι ουσιαστικά το κλειδί του map στο οποίο αποθηκεύονται οι ρυθμίσεις στο SharedPreferences file. Για παράδειγμα φανταστείτε αυτό το Map κάπως έτσι
__key__=__value__
location=734077
surname=Chaikalis
name=Theodore

Τα resources κειμένου για το το ``res/values/strings.xml`` είναι : 

```
<string name="pref_location_label">Location</string>
<string name="pref_location_key" translatable="false">location</string>
<string name="pref_location_default" translatable="false">734077</string>
```

##### Εμφάνιση ρυθμίσεων στην ειδική οθόνη

Για να εμφανιστεί η νέα μας ρύθμιση θα πρέπει να προσθέσουμε κώδικα στην κλάση ``SettingsActivity``.

Μέθοδος ``onCreate(Bundle savedInstanceState)``:

```
 @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        addPreferencesFromResource(R.xml.pref_general);
        bindPreferenceSummaryToValue(findPreference(getString(R.string.pref_location_key)));
}
```

Αφού έχουμε προσθέσει την κατάλληλη ρύθμιση, είμαστε πλέον σε θέση να διαβάζουμε την τιμή της ρύθμισης αυτής έτσι ώστε να καλούμε το Web Service του OpenWeatherMap API με τον επιθυμητό κωδικό πόλης και έτσι να βλέπουμε την επιθυμητή πρόβλεψη καιρού. 
Για να γίνει αυτό θα πρέπει να διορθώσουμε τον τρόπο κλήσης του Web Service. Θυμίζουμε ότι η υλοποίηση αυτή βρίσκεται στην κλάση ForecastFragment. Πρέπει να:

1. Δημιουργήσουμε μια νέα μέθοδο με όνομα ``updateWeather``
2. Να διορθώσουμε τη μέθοδο ``onOptionsItemSelected(MenuItem item) ``

```
private void updateWeather() {
        SharedPreferences prefs = PreferenceManager.getDefaultSharedPreferences(getActivity());
        String location = prefs.getString(getString(R.string.pref_location_key), getString(R.string.pref_location_default));
        FetchWeatherTask weatherTask = new FetchWeatherTask();
        weatherTask.execute(location);
}

@Override
    public boolean onOptionsItemSelected(MenuItem item) {
        int id = item.getItemId();

        if(id == R.id.action_refresh){
            updateWeather();
            return true;
        }
        return super.onOptionsItemSelected(item);
}

```

Ας εκμεταλευτούμε την ευκαιρία της δημιουργίας της μεθόδου updateWeather για να προσθέσουμε την κλήση της **κατά τη δημιουργία** του Activity, έτσι ώστε να ξεκινάει η εφαρμογή με τα κανονικά και όχι με τα dummy δεδομένα. Στην ίδια κλάση (ForecastFragment) κάνουμε override τη μέθοδο ``onStart()``

```
@Override
    public void onStart() {
        super.onStart();
        updateWeather();
    }
```

θα πρέπει επίσης να διορθώσουμε την δημιουργία του ArrayAdapter έτσι ώστε να μην αρχικοποιείται με Dummy Data, αλλά με ένα κενό ArrayList.

ΆΣΚΗΣΗ:

Προσθήκη μιας ακόμα επιλογής ρυθμίσεων για μετατροπή θερμοκρασίας απο Κελσίου (Weather API default), σε Fahrenheit

## Share Functionality !

Θα προσθέσουμε λειτουργία sharing στην εφαρμογή μας εκμεταλευόμενοι τα Implicit Intents

1. Προσθέτουμε ένα νέο αρχείο ```detailfragment``` στον φάκελο /res/menu καθώς και τα κατάλληλα string resources. Το menu item αυτό θα εμφανίζεται σαν action στο επάνω taskbar και θα είναι ορατό συνεχώς.

![Share activity](https://github.com/UomMobileDevelopment/Lesson05-material/blob/master/shareActivity-1.png)

```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"  >

    <item
        android:id="@+id/action_share"
        android:title="@string/action_share"
        app:showAsAction="always"
        app:actionProviderClass="android.support.v7.widget.ShareActionProvider" />

</menu>
```

2. Στη συνέχεια θα πρέπει να προσθέσουμε κώδικα στην inner κλάση ```DetailFragment``` (ίσως την έχετε ως ```PlaceholderFragment```) της κλάσης ```DetailActivity```. 
Αναλυτικά

3. Προσθέτουμε απαραίτητες ιδιότητες που θα μας χρειαστούν
```
 private static final String LOG_TAG = DetailFragment.class.getSimpleName();
        private static final String FORECAST_SHARE_HASHTAG = " #SunshineApp";
        private String forecastString;
```

4. Δηλώνουμε ότι το fragment αυτό έχει δικό του μενού.
```
 public DetailFragment() {
            setHasOptionsMenu(true);
        }
```

5. Αλλάζουμε τον κώδικα δημιουργίας της μεταβλητής forecastString, καθώς τώρα είναι ιδιότητα ορατή σε όλη την κλάση και όχι τοπική μεταβλητή. Αυτό το κάνουμε γιατί θα χρειαστούμε την τιμή του String και σε άλλη μέθοδο. Είναι το String της πρόβλεψης που θα περάσουμε σαν μήνυμα στον ShareProvider. Είναι αυτό το κείμενο που τελικά θα γίνει share.
```
if(intent != null && intent.hasExtra(Intent.EXTRA_TEXT)){
                forecastString = intent.getStringExtra(Intent.EXTRA_TEXT);
                TextView detailText = (TextView)rootView.findViewById(R.id.detail_text);
                detailText.setText(forecastString);
            }
```

6. Δημιουργούμε τη βοηθητική μέθοδο ``createShareForacastIntent``, η οποία αναλαμβάνει να δημιουργήσει το Implicit Intent και να το γεμίσει με τα απαραίτητα δεδομένα.
```
 private Intent createShareForacastIntent(){
            Intent shareIntent = new Intent(Intent.ACTION_SEND);
            //clear activity and not put it on the activity stack.
            //If this flag is not present, then after sharing the screen will return to the sharing app
            //and not in our app!!!
            //FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET is DEPRECATED, FLAG_ACTIVITY_NEW_DOCUMENT should be used instead
            // shareIntent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET);
            shareIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_DOCUMENT);
            shareIntent.setType("text/plain");
            shareIntent.putExtra(Intent.EXTRA_TEXT, forecastString + FORECAST_SHARE_HASHTAG);
            Log.e(LOG_TAG, "share intent created");
            Log.e(LOG_TAG, "string extra = "+shareIntent.getStringExtra(Intent.EXTRA_TEXT));

            return shareIntent;
        }
```

7. Επικαλύπτουμε (Override) τη μέθοδο ``onCreateOptionsMenu`` έτσι ώστε να δηλώσουμε τι θέλουμε να γίνεται στη δημιουργία του menu επιλογών. Αυτό που δηλώνουμε είναι οτι θέλουμε στο πάτημα του SHARE εικονιδίου να ξεκινάει ο Share Action Provider του λειτουργικού στο οποίο έχουμε περάσει το Intent (``.setShareIntent(createShareForacastIntent());``) με τις χρήσιμες πληροφορίες που θέλουμε να γίνουν Share. 
```
@Override
        public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
            //Inflate the menu, this adds items to the action bar if it is not present
            inflater.inflate(R.menu.detailfragment, menu);

            MenuItem menuItem = menu.findItem(R.id.action_share);

            ShareActionProvider shareActionProvider = (ShareActionProvider) MenuItemCompat.getActionProvider(menuItem);

            if(shareActionProvider != null){
                shareActionProvider.setShareIntent(createShareForacastIntent());
                Log.e(LOG_TAG, "intent set to share action provider ");
            }
            else{
                Log.e(LOG_TAG, "Share action provider is null?");
            }
            super.onCreateOptionsMenu(menu, inflater);
        }
```

#### Είμαστε έτοιμοι! Ας κάνουμε το πρώτο δικό μας μήνυμα, Share  


![Share activity](https://github.com/UomMobileDevelopment/Lesson05-material/blob/master/shareActivity-2.png)

