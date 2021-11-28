# Architecture_Lab_1

**_Κώνστας Νικόλαος 9723\
Εργασήριο Α, Ομάδα 3_**

## Ερώτημα 1

Ανοίγωντας το αρχείο _[starter\_se.py](https://github.com/konstasn/Architecture_Lab_1/blob/main/configs/starter_se.py)_ διακρίνουμε τα εξής βασικά χαρακτηριστικά του συστήματος:
* Αρχικά πληκτρολογώντας `--cpu="minor"` κατά την αρχικοποίηση της προσομοίωσης δημιουργείται ένας επεξεργαστής βασισμένος στο μοντέλο MinorCPU με τις ακόλουθες caches: {L1I, L1D, WalkCache, L2}
* Το μέγεθος του cache line είναι **64Bytes**
* To σύστημα έχει clock domain **1GHz** και voltage domain **3.3V**
* Παρατηρούμε πως το _default cpu_type_ είναι **AtomicSimpleCPU**
* Ο επεξεργασής έχει **1 πυρήνα** και τρέχει σε συχνότητα **1GHz**
* Η μνήμη έχει μέγεθος **2GBytes** τρέχει σε **2 channels** και είναι τύπου **DDR3_1600_8x8**

Για να τροποποίησουμε τη συχνότητα που λειτουργεί το σύστημα υπάρχουν 2 τρόποι:
* Προσθέτουμε στην εντολή που τρέχουμε στο terminal το tag `--cpu-freq` π.χ.
  ```console
  $ ./build/ARM/gem5.opt configs/example/arm/starter_se.py --cpu-freq="5GHz" "tests/test-progs/hello/bin/arm/linux/hello"
  ```
* Εναλλακτικά μπορούμε να αλλάξουμε την _deafult_ συχνότητα στο _[starter\_se.py](https://github.com/konstasn/Architecture_Lab_1/blob/main/configs/starter_se.py)_ από 1GHz στην επιθυμητή, π.χ. 5GHz
  ```python
  parser.add_argument("--cpu-freq", type=str, default="5GHz")
  ```
## Ερώτημα 2

**sim_seconds**: _"# Number of seconds simulated"_\
Δηλάδη καταγράφει για πόσα δευτερόλεπτα έτρεξε το πρόγραμμα στο σύστημα που προσομοιώνουμε.

**sim_insts**: _"# Number of instructions simulated"_\
Μετρά τα instructions που εκτελεί το προσομοιωμένο σύστημα.

**host_inst_rate**: _"# Simulator instruction rate (inst/s)"_\
Το πλήθος των εντολών που εκτελεί το προσομοιωμένο σύστημα ανά δευτερόλεπτο(πραγματικού χρόνου).

## Ερώτημα 3

Από το _[stats.txt](https://github.com/konstasn/Architecture_Lab_1/blob/main/hello_results/stats.txt)_ καταφράφουμε τα παρακάτω misses:\
IL1_misses = 327\
DL1_misses = 177\
L2_misses = 474\
Total_Instructions = 5027\
\
Για 1 cycle cache hit/instruction execution, L1-miss_penalty = 6 cycles και L2-miss_penalty = 50 cycles, είναι:\
\
<img src="https://latex.codecogs.com/svg.latex?\space\;CPI=1+\frac{(IL1\_misses+DL1\_misses)*6+L2\_misses*50}{Total\_Instructions}" title="CPI" />\
<img src="https://latex.codecogs.com/svg.latex?\space\;CPI=1+\frac{(327+177)*6+474*50}{5027}" title="CPI" />\
<img src="https://latex.codecogs.com/svg.latex?\space\;CPI=6.316" title="CPI" />

_\*Από το αρχείο [stats.txt](https://github.com/konstasn/Architecture_Lab_1/blob/main/hello_results/stats.txt) παίρνουμε το πραγματικό CPI = 6.991048._
     
## Ερώτημα 4

### Βιβλιογραφική αναφορά

#### [SimpleCPU](https://www.gem5.org/documentation/general_docs/cpu_models/SimpleCPU)
Το SimpleCPU είναι ένα πλήρως λειτουργικό, in-order μοντέλο κατάλληλο για περιπτώσεις, όπου δε χρειάζεται ένα λεπτομερές μοντέλο. Τέτοιες περιπτώσεις είναι "warm-up periods", "client systems that are driving a host" ή μια απλή δοκιμή της λειτουργικότητας ενός προγράμματος. Πρόσφατα το μοντέλο ξαναγράφτηκε, ώστε να υποστηρίζει το καινούριο σύστημα μνήμης, και πλέον χωρίζεται σε τρεις κλάσεις:
1. BaseSimpleCPU
2. AtomicSimpleCPU
3. TimingSimpleCPU
  
#### [O3CPU](https://www.gem5.org/documentation/general_docs/cpu_models/O3CPU)
To O3CPU είναι ένα καινούριο out-of-order μοντέλο του gem5 το οποίο βασίζεται, σε γενικές γραμμές, στον [Alpha 21264](https://en.wikipedia.org/wiki/Alpha_21264). Το συγκεκριμένα μοντέλο έχει pipeline με 5 στάδια:
* Fetch
* Decode
* Rename
* Issue/Execute/Writeback
* Commit


#### [MinorCPU](https://www.gem5.org/documentation/general_docs/cpu_models/minor_cpu)
Το MinorCPU είναι ένα in-order μοντέλο επεξεργαστή με σταθερό pipeline, αλλά διαμορφώσιμα data structures και execution behaviour. Είναι φτιαγμένος για χρήση με αυστηρά in-order execution behaviour. Ένα πολύ χρήσιμο χαρακτηριστικό του συγκεκριμένου μοντέλου είναι το visualisation της θέσης ενός instruction μέσα στο pipeline.

### Πρόγραμμα σε C

Για το συγκεκριμένο ερώτημα χρησιμοποιήθηκ ένα απλό πρόγραμμα σε C(_[fib_test.c](https://github.com/konstasn/Architecture_Lab_1/blob/main/my_test_program/fib_test.c)_) το οποίο υπολογίζει και τυπώνει τους πρώτους 20 αριθμούς τις ακολουθίας Fibonacci.\
Στη συνέχεια πληκτρολογώντας στην κονσόλα την εντολή
```console
$ arm-linux-gnueabihf-gcc --static fib_test.c -o fib_test arm
```
κάνουμε στατικό compile του προγράμματος και παράγουμε ενα executable αρχείο για σύστημα ARM(_[fib_test_arm](https://github.com/konstasn/Architecture_Lab_1/blob/main/my_test_program/fib_test_arm)_).

#### Ερώτημα 4.a

Αρχικά τρέχουμε το test/πρόγραμμα για **MinorCPU** με την εντολή:
```console
 $ ./build/ARM/gem5.opt -d ../fib_test_minor configs/example/se.py --cpu-type=MinorCPU  --caches --cmd=../fib_test_arm
```
και στη συνέχεια για **TimingSipleCPU** με την εντολή:
```console
 $ ./build/ARM/gem5.opt -d ../fib_test_timing configs/example/se.py --cpu-type=TimingSimpleCPU  --caches --cmd=../fib_test_arm
```
Από τα αρχεία _[test_minorCpu->stats.txt](https://github.com/konstasn/Architecture_Lab_1/blob/main/test_minorCpu/stats.txt)_ και _[test_timingSimpleCpu->stats.txt](https://github.com/konstasn/Architecture_Lab_1/blob/main/test_timingSimpleCpu/stats.txt)_ παίρνουμε τα παρακάτω αποτελέσματα:
| CPU Model | Runtime(ticks) | Runtime(seconds) | Host runtime(seconds) |
|:---------:|:--------------:|:----------------:|:---------------------:|
| MinorCPU | 504411000 | 0.000504 | 2.46 |
| TimingSimpleCPU | 1117639000 | 0.001118 | 0.78 |  
  
Παρατηρούμε πως στο test μας με το συγκεκριμένο configuration ο **MinorCPU** είναι περίπου 2 φορές γρηγορότερος ενώ ο **TimingSimpleCPU** τρέχει περίπου 3 φορές γρηγορότερα στο host system.

#### Ερώτημα 4.b.i

Στο συγεκριμένο υποερώτημα μεταβάλουμε τη συχνότητα του CPU για να δούμε πως αυτή η μεταβολή θα επηρεάσει το χρόνο εκτέλεσης των δύο μοντέλων CPU.
Και στις δύο περιπτώσεις θέτουμε τη συχνότητα του CPU στα 5GHz( η default τιμή ήταν 2GHz όπως βλέπουμε στα αρχεία _[test_minorCpu->config.ini](https://github.com/konstasn/Architecture_Lab_1/blob/main/test_minorCpu/config.ini)_ και _[test_timingSimpleCpu->config.ini](https://github.com/konstasn/Architecture_Lab_1/blob/main/test_timingSimpleCpu/config.ini)_).
Για το **MinorCPU** εκτελούμε την εντολή:
```console
$ ./build/ARM/gem5.opt -d ../fib_test_minor_oc configs/example/se.py --cpu-type=MinorCPU --cpu-clock="5GHz" --caches --cmd=../fib_test_arm
```
, ενώ για τον **TimingSimpleCPU** εκτελούμε την εντολή:
```console
$ ./build/ARM/gem5.opt -d ../fib_test_timing_oc configs/example/se.py --cpu-type=TimingSimpleCPU --cpu-clock="5GHz" --caches --cmd=../fib_test_arm
```
Από τα αντίστοιχα αρχεία στατιστικών _[test_minorCpu_freq->stats.txt](https://github.com/konstasn/Architecture_Lab_1/blob/main/test_minorCpu_freq/stats.txt)_ και _[test_timingSimpleCpu_freq->stats.txt](https://github.com/konstasn/Architecture_Lab_1/blob/main/test_timingSimpleCpu_freq/stats.txt)_ έχουμε:

| CPU Model | Runtime(ticks) | Runtime(seconds) | Host runtime(seconds) |
|:---------:|:--------------:|:----------------:|:---------------------:|
| MinorCPU @ 2GHz | 504411000 | 0.000504 | 2.46 |
| MinorCPU @ 5GHz | 221548200 | 0.000222 | 2.42 |
| TimingSimpleCPU @ 2GHz | 1117639000 | 0.001118 | 0.78 | 
| TimingSimpleCPU @ 5GHz | 464119000 | 0.000464  | 0.79 | 
  
Όπως ήταν αναμενόμενο αυξάνοντας τη συχνότητα λειτουργίας του επεξεργαστή κατά 2.5 φορές είδαμε αντίστοιχη πτώση και στο χρόνο εκτέλεσης, ενώ ο χρόνος που χρειάστηκε το host system για να τρέξει της προσομοίωσης έμεινε πρακτικά σταθερός. Συγκεριμένα ο **MinorCPU** έγινε 2.27 φορές γρηγορότερος ενώ ο **TimingSimpleCPU** επιταχύνθηκε 2.41 φορές. Παρατηρούμε πως ο **TimingSimpleCPU** επωφελήθηκε λίγο παραπάνω από την αύξηση της συχνότητας αλλά συνέχισε να είναι περίπου 2 φορές αργότερος από τον **MinorCPU**.

#### Ερώτημα 4.b.ii

Τώρα θα αλλάξουμε τη μνήμη που χρησιμοποιεί το σύστημα. H default μνήμη είναι **DDR3_1600_8x8** την οποία θα αντικαταστήσουμε με **DDR4_2400_8x8**.
Για το MinorCPU εκτελούμε:
```console
$ ./build/ARM/gem5.opt -d ../fib_test_minor_mem configs/example/se.py --cpu-type=MinorCPU --mem-type=DDR4_2400_8x8 --caches --cmd=../fib_test_arm
```
και για τον TimingSimpleCPU:
```console
$ ./build/ARM/gem5.opt -d ../fib_test_timing_mem configs/example/se.py --cpu-type=TimingSimpleCPU --mem-type=DDR4_2400_8x8 --caches --cmd=../fib_test_arm
```
Απότ τα αρχεία _[test_minorCpu_mem->stats.txt](https://github.com/konstasn/Architecture_Lab_1/blob/main/test_minorCpu_mem/stats.txt)_ και _[test_timingSimpleCpu_mem>stats.txt](https://github.com/konstasn/Architecture_Lab_1/blob/main/test_timingSimpleCpu_mem/stats.txt):

| CPU Model | Runtime(ticks) | Runtime(seconds) | Host runtime(seconds) |
|:---------:|:--------------:|:----------------:|:---------------------:|
| MinorCPU - DDR3_1600_8x8 | 504411000 | 0.000504 | 2.46 |
| MinorCPU - DDR4_2400_8x8  | 503111000 | 0.000503 | 2.44 |
| TimingSimpleCPU - DDR3_1600_8x8  | 1117639000 | 0.001118 | 0.78 | 
| TimingSimpleCPU - DDR4_2400_8x8 | 1117264000 | 0.001117  | 0.78 | 
  
Και στα δύο συστήματα παρατηρούμε μια αμελητέα πτώση του χρόνου εκτέλεσης ενώ και πάλι ο χρόνος στο host system δεν αλλάζει.

## Βιβλιογραφία
[gem5.org](https://www.gem5.org/documentation/general_docs/cpu_models/SimpleCPU)

## Κριτική

Η παρούσα εργασία ήταν αρκετά βοηθητική ούτως ώστε να εξοικειωθώ με το περιβάλλον εργασίας του gem5 και τις βασικές του λειτουργίες, καθώς και με το github και τη σύνταξη αναφοράς στη Markdown. Επίσης το έτοιμο VM που μοιραστήκατε μαζί μας στο e-learning με βοήθησε να εξοικονομήσω χρόνο και να επικεντρωθώ στην ουσία της εργασίας. Θεωρώ πως η εργασία μέτριας δυσκολίας και μπορούσε να υλοποιηθεί από ανεξοικείωτους με το gem5 φοιτητές, αλλά θα ήταν καλύτερο να είχε προηγηθεί μια παρουσίαση του εργαλείου όπου θα τρέχαμε κάποια βασικά παραδείγματα. Ακόμη πιστεύω πως η εργασία θα έπρεπε να είχε ανακοινωθεί/δημοσιευτεί νωρίτερα, καθώς σε αυτό το σύντομο χρονικό διάστημα συνέπεσε με άλλες εργασίες και υποχρεώσεις. Τέλος νομίζω πως υπάρχει ένα λάθος στο pdf στο σημείο που δίνεται αυτή η εντολή `$ ./build/ARM/gem5.opt configs/example/se.py --cpu=MinorCPU –caches
tests/test-progs/hello/bin/arm/linux/hello `, αφού για να τρέξει στο σύστημα μου χρειάστηκε να την μετατρέψω έτσι `$ ./build/ARM/gem5.opt configs/example/se.py --cpu-type=MinorCPU -–caches --cmd=tests/test-progs/hello/bin/arm/linux/hello` .
