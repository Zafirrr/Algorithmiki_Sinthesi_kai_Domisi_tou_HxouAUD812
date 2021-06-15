# Algorithmiki_Sinthesi_kai_Domisi_tou_Hxou_AUD812
Ζαφείρης Γεωργόπουλος-Χατζημανώλης ΤΧ2005041 ΕΡΓΑΣΙΑ EΞΑΜΗΝΟΥ

Χρησιμοποιούμε generative patterning για να δημιουργήσουμε ένα self-playing κομμάτι. Τα κύρια εργαλεία για να το πετύχουμε είναι:

Ppar

Ενσωματώνει πολλαπλά event streams, έτσι ώστε να σχηματίζουν ένα single output stream με όλα τα events τους σε χρονική σειρά. Όταν ένα stream τελειώνει, τα άλλα streams ενσωματώνονται περαιτέρω έως ότου όλα έχουν τελειώσει.

Pbind

Το Pbind συνδυάζει πολλαπλά value streams σε ένα event stream. Κάθε value stream αντιστοιχεί σε ένα ή περισσότερα keys στο event stream που προκύπτει. Καθορίζει μια ροή από Events μέσω διάφορων patterns που συνδέονται με διάφορα keys στο Event. Τα patterns που συνδέονται με τα keys αναφέρονται ως value patterns και το ίδιο το Pbind ονομάζεται event pattern.


SynthDef και Event

Τα keys που χρησιμοποιούνται σε ένα Pbind καθορίζονται από το SynthDef που χρησιμοποιείται και τη δομή του default μηχανισμού που παρέχεται από το Event.

Το SynthDef εκχωρεί ένα όνομα σε μια διασύνδεση από unit generators που θα εκτελεστούν ως synth σε έναν σερβερ. Εκχωρεί επίσης ονόματα ελέγχου στα control inputs του synth. Στο ακόλουθο παράδειγμα το SynthDef \ test έχει control inputs τα: out, freq, amp, nharms, pan και gate.

SynthDef(\test, { | out, freq = 440, amp = 0.1, nharms = 10, pan = 0, gate = 1 |
    var audio = Blip.ar(freq, nharms, amp);
    var env = Linen.kr(gate, doneAction: Done.freeSelf);
    OffsetOut.ar(out, Pan2.ar(audio, pan, env) );
}).add;

Το SynthDef πρέπει να ληφθεί στον σερβερ στον οποίο θα εκτελεστεί. Χρησιμοποιούμε το .add αντί για .send για να βεβαιωθούμε ότι τυχόν μοτίβα που χρησιμοποιούν αυτό το SynthDef έχουν πληροφορίες σχετικά με τα διαθέσιμα control inputs. Εναλλακτικά, το .store μπορεί να χρησιμοποιηθεί για να αποθηκεύσουμε το SynthDef στο δίσκο και να προσθέσουμε το SynthDesc στη βιβλιοθήκη.

Ένα event είναι ένα Dictionary που καθορίζει μια ενέργεια που πρέπει να ληφθεί για να παίξει και μια χρονική αύξηση που θα επιστραφεί ως απόκριση στο delta. Τα events μπορούν να γραφτούν ως μια σειρά ζευγών τιμών κλειδιών που περικλείονται σε παρενθέσεις. Οι κενές παρενθέσεις δημιουργούν ένα κενό event.

Από προεπιλογή, τα events παίζουν synths σε έναν σέρβερ. Τέτοια events χρησιμοποιούν τα ακόλουθα keys:


instrument (\default) - Το synthdef που θα παιχτεί

variant (nil, optional) - Το σύνολο προεπιλογών ελέγχου παραλλαγής για χρήση

server (Server.default) - Ο σέρβερ που παίζει το synth

delta (function) - Ο χρόνος μέχρι το επόμενο event σε μια σειρά από events, που γενικά καθορίζεται έμμεσα μέσω του dur
