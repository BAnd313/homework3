# Homework 3 - Laboratorio Ciberfisico

Terzo homework del corso Laboratorio Ciberfisico, Università degli studi di Verona.

## Consegna

Homework 3 è composto da 4 parti:
1. installazione di ORB_SLAM2
2. esecuzione di ORB_SLAM2 su una rosbag registrata con un drone volante
3. creazione di una point cloud
  * Modificare opportunamente il codice di ORB_SLAM2 in modo che venga salvata in un file .pcd la point cloud corrispondente alla ricostruzione dei punti 3D generata dall’algoritmo di SLAM. Assicurarsi che il file .pcd creato abbia un formato compatibile con la libreria PCL
4. clustering dei punti contenuti nella point cloud generata al punto 3
  * Clusterizzare i punti della point cloud in base alla distanza Euclidea utilizzando opportuni valori di soglia

## Cosa cambia?

Questo progetto contiene una versione modificata del programma ORB_SLAM2 (https://github.com/raulmur/ORB_SLAM2). La modifica comporta il salvataggio di una point cloud in un file `pointcloud.pcd` durante la chiusura del programma. I file modificati sono:
* `src/System.cc` aggiunta la funzione `SavePCD(...)` che effettua il salvataggio della point cloud su file, commentate 2 righe di codice che mandavano il programma in deadlock
* `include/System.h` aggiunta la dichiarazione della funzione sopra descritta
* `Examples/ROS/ORB_SLAM2/src/ros_stereo.cc` aggiunto il richiamo della funzione sopra descritta poco prima della chiusura del programma

## Cosa c'è di nuovo?

In aggiunta alla versione modificata di ORB_SLAM2 c'è un programma (ClusterExtraction) che permette la clusterizzazione dei punti della point cloud in base alla distanza Euclidea. Il programma è formato da:
* `cluster_extraction.cpp` sorgente del programma, basato sul suggerimento della [documentazione ufficiale pcl](http://www.pointclouds.org/documentation/tutorials/cluster_extraction.php) con aggiunta dei parametri da riga di comando
* `CMakeLists.txt` file cmake per la compilazione del programma 

## Compilazione

Il tutto è stato testato su `Ubuntu 16.04`. Le dipendenze sono le medesime del software ORB_SLAM2, si possono trovare sulla [pagina git](https://github.com/raulmur/ORB_SLAM2#2-prerequisites) corrispondente. Assicurarsi di averle installate prima di procedere.

Fatto ciò, è sufficiente aprire un terminale, posizionarsi nella cartella `homework3` e lanciare lo script `build.sh`

## Esecuzione ORB_SLAM2

Per prima cosa scaricare la rosbag utilizzata per questo homework, reperibile [qui](http://robotics.ethz.ch/~asl-datasets/ijrr_euroc_mav_dataset/vicon_room1/V1_01_easy/V1_01_easy.bag). Lanciare 3 terminali distinti:

1. Lanciare roscore:
```
roscore
```

2. Posizionarsi nella cartella `ORB_SLAM2_rev` ed eseguire i seguenti comandi:
```
export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:$(pwd)/Examples/ROS
rosrun ORB_SLAM2 Stereo Vocabulary/ORBvoc.txt Examples/Stereo/EuRoC.yaml true
```

3. Posizionarsi nella cartella dov'è stata scaricata la bag e lanciarla:
```
rosbag play --pause V1_01_easy.bag /cam0/image_raw:=/camera/left/image_raw /cam1/image_raw:=/camera/right/image_raw
```

Dal terminale 3 premere `SPAZIO` per avviare la bag e terminata l'esecuzione premere `Ctrl + C` sul terminale 2.
Verrà salvata la point cloud in `ORB_SLAM2_rev/pointcloud.pcd`

![Execution](media/ORB_SLAM2.png)

Per verificare il corretto salvataggio del file e vedere graficamente la point cloud, utilizzare il seguente comando:
```
pcl_viewer pointcloud.pcd
```

![Pointcloud](media/pointcloud.png)

![Pointcloud3D](media/1.gif)

## Esecuzione ClusterExtraction

Una volta ottenuto il file .pcd è possibile eseguirne la clusterizzazione.

Aprire un terminale, posizionarsi nella cartella `ClusterExtraction/build` ed eseguire il comando seguente:
```
./cluster_extraction pointcloud.pcd 0.30
```
Sostituire `pointcloud.pcd` con il percorso del file corretto.
L'ultimo parametro è il valore di tolleranza, è possibile ometterlo. In tal caso assumerà il valore di default di `0.28`

Terminata l'esecuzione verranno generati diversi cluster. Per visualizzarli graficamente, utilizzare il seguente comando:
```
pcl_viewer cloud_cluster_*
```

**ATTENZIONE**: prima di avviare nuovamente `cluster_extraction` con un valore di tolleranza diverso dal precedente, è consigliato eliminare tutti i file `cloud_cluster_*` così da avere poi solamente quelli generati dall'ultimo comando.

## Screenshots

Cluster Euclideo con tolleranza 0.28

![Cluster_tolerance_0.28](media/cluster0.28.png)

![Cluster_tolerance_0.28_3D](media/2.gif)

Cluster Euclideo con tolleranza 0.40

![Cluster_tolerance_0.40](media/cluster0.40.png)

![Cluster_tolerance_0.40_3D](media/3.gif)

## Autori

* **Andrea Benini** - [BAnd313](https://github.com/BAnd313)

## Licenza

Software distribuito sotto licenza GNU LGPLv3. Vedi `LICENSE` per ulteriori informazioni.
