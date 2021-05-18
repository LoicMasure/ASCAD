## The ASCADv2 database

We provide source code implementation of a protected AES-128 encryption and decryption for 32-bit Cortex-M ARM, which can be found on the following github repository: [ANSSI-FR/secAES-STM32](https://github.com/ANSSI-FR/SecAESSTM32). This implementation combines two side channel countermeasures: affine masking and shuffling. To test it, we measured the power consumption of a STM32 Cortex M4 microcrontroller (STM32F303RCT7) during 800,000 AES encryptions with random keys and random plaintexts. Traces have been acquired with a ChipWhisperer board [CW308T-STM32F](https://wiki.newae.com/CW308T-STM32F) by underclocking the STM32 clock to 4 MHz and acquired through an oscilloscope with a 50,000,000 sample-per-second rate. The measured traces consist of 1,000,000 samples points, encompassing the whole AES encryption. The resulting dataset will be made publicly available uppon acceptance of the submission.

## <a name="getting-ascadv2"> Getting the ASCADv2 databases and the trained models 

In the new folder, first download the data packages with the raw data by using:

<pre>
$ cd STM32_AES_v2
$ mkdir -p ASCAD_data/ASCAD_databases
$ cd ASCAD_data/ASCAD_databases
</pre>
 

Please be aware that all these steps should **download around 807 GB** of data.
You can selectively download only the [extracted database](https://www.data.gouv.fr/fr/datasets/r/a6cf925c-079c-4468-a723-d94bce6c31f8) that weights "only" 7 GB.


Once this step is over, you can download the trained models:

<pre>
$ mkdir ../ASCAD_trained_models
$ chmod +x download_models.sh
$ cd ../ASCAD_trained_models
$ ../../download_models.sh
</pre>
The first model corresponds to a multitask ResNet model that predicts all the intermediate mask values (multiplicative mask, additive mask, shuffled masked sbox output for each index i in [1..16], shuffle index for i in [1..16]). The second model ignores that the implementation has shuffled the sbox outputs and classifies directly the multiplicative mask, additive mask, and the masked sbox outputs without shuffle. More details about the two models may be found in the submission.
## Test the trained models

<pre>
$ cd ../../..
$ python ASCAD_test_models.py ./STM32_AES_v2/example_test_models_params # if you want to test the first trained model
$ python ASCAD_test_models.py ./STM32_AES_v2/example_test_models_without_permind_params # if you want to test the second trained model
</pre>

The script performs a recombination of the labels probabilities and computes a Maximum Likelyhood Estimation (MLE) for each possible value of the key bytes. Then the rank of the correct key (obtained from the MLE order) is displayed for each byte of the key. The script validates the success of our attack since all the correct key byte values are ranked first after approximatively 120 test traces for the first trained model, and 250 test traces for the second model. 

## Perform the attack from the raw dataset
If you want to test all the steps of the attack from scratch, you can extract the 15,000 points of interest from the raw dataset by using:

<pre>
$ python ASCAD_generate.py ./STM32_AES_v2/example_generate_params
</pre>

Then you can train each of the models by using:

<pre>
$ python ASCAD_train_models.py ./STM32_AES_v2/example_train_models_params
$ python ASCAD_train_models.py ./STM32_AES_v2/example_train_models_without_permind_params
</pre>

## Raw data files hashes

The data files SHA-1 hash values are the following:

* 8de295c8c18d033b13c03474189c0976a49ca993  ascadv2-extracted.h5
* 51240368a3b392660e0f9af41fb327d1c0e4bccc  ascadv2-multi-resnet-60epochs.h5
* 45ec8daab46ad72dd633526771f0b4d4f206147f  ascadv2-multi-resnet-wo-permind-60epochs.h5
* 4352910b4f4dea2de872f2122bb530582a238e02  ascadv2-stm32-conso-raw-traces1.h5
* b32a239bbdb3d683833d625a109f17a55d36f24a  ascadv2-stm32-conso-raw-traces2.h5
* 10c4cdc37578f8ef6bd59608345548ffd6ba3771  ascadv2-stm32-conso-raw-traces3.h5
* f0aacc9587e94532f86a26355db81e5b4ab2848b  ascadv2-stm32-conso-raw-traces4.h5
* 4158149ac9a0bd7a8dda758ddb45927e7015b831  ascadv2-stm32-conso-raw-traces5.h5
* 5fd51946c8552514f4c56e8730a0f7cb9ca1ac5e  ascadv2-stm32-conso-raw-traces6.h5
* ab5e6fc117b11aae733ce61c6424f7d55a487039  ascadv2-stm32-conso-raw-traces7.h5
* ef72fb2a388099ef41fbdbb0ccaefc287f048290  ascadv2-stm32-conso-raw-traces8.h5

