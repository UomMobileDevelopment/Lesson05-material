# Lesson05-material

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

