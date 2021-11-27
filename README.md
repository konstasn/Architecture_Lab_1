# Architecture_Lab_1

## Ερώτημα 1

Ανοίγωντας το αρχείο _[starter\_se.py](https://github.com/konstasn/Architecture_Lab_1/blob/main/starter_se.py)_ διακρίνουμε τα εξής βασικά χαρακτηριστικά του συστήματος:
* Αρχικά πληκτρολογώντας `--cpu="minor"` κατά την αρχικοποίηση της προσομοίωσης δημιουργείται ένας επεξεργαστής βασισμένος στο μοντέλο MinorCPU με τις ακόλουθες caches: {L1I, L1D, WalkCache, L2}
* Το μέγεθος του cache line είναι 64Bytes
* To σύστημα έχει clock domain 1GHz και voltage domain 3.3V
* Παρατηρούμε πως το _default cpu_type_ είναι **AtomicSimpleCPU**
* Ο επεξεργασής έχει 1 πυρήνα και τρέχει σε συχνότητα **1GHz**
* Η μνήμη έχει μέγεθος 2GBytes τρέχει σε 2 channels και είναι τύπου _DDR3_1600_8x8_

Για να τροποποίησουμε τη συχνότητα που λειτουργεί το σύστημα υπάρχουν 2 τρόποι:
* Προσθέτουμε στην εντολή που τρέχουμε στο terminal το tag `--cpu-freq` π.χ.
  ```shell
  $ ./build/ARM/gem5.opt configs/example/arm/starter_se.py --cpu-freq="5GHz" "tests/test-progs/hello/bin/arm/linux/hello"
  ```
  
