Promotech-CNN is a Bacterial Promoter Prediction Tool built using Dilated Convolution Neural Networks.

The body of the code was built using a Class module that read fasta files to retrieve DNA sequences. Once read, the sequences are converted to one-hot-coding and passed to the CNN for building and training. Our CNN architecture was guided by the BPNet architecture found at https://www.nature.com/articles/s41588-021-00782-6, however, this solution does not use as many layers and have several tweeks in architecture.

Environment:

	-- Operating System: Linux
	-- Python: 3.8.2
	-- tensorflow: 2.5.0
	-- numpy: 1.2.0
	-- pandas: 1.2.4


About the Files:

	-- Promotech-CNN-20210720.h5 - H5 module built from the trained network. This is loaded during predictions
	-- PromotechCNN.py	- Base Class module that is used to build and train a netwok if required. It handles promoter sequence conversion before they are fed into the Neural Network. The main prediction method is processed here.
	-- PromotechCNNPredict.py - Main file to load CNN module and obtain predictions for sequences in FASTA format

## Making Predictions:
- Predictions are made after loading the H5 data file and then running the prediction on an input promoter sequence file.

		-- You may run predictions on fasta files or text files with only one 40nt sequence per line. Sequences must contain only A, C, G and T.
			python PromotechCNNPredict.py Promotech-CNN-20210720.h5 sequences.fasta

- The output is a comma-delimited text file with four columns: sequence, probability of not being a promoter, probability of being a promoter and the prediction (0 - non promoter, 1 - promoter). The first row contains the column headers.
```
Seq,Neg_C,Pos_C,Pred
CAACCAGCAAGAGAACATCTGGCCTGCGGGCACCACTGCT,0.9882957,0.0117043005,0
GGCCTGCGGGCACCACTGCTACAGTGGTGGTCACTGGCAA,0.9797492,0.020250821,0
ACAGTGGTGGTCACTGGCAAACAAGATCGCGACGAAAGCC,0.9842748,0.015725216,0
CCGCGACCTTTGCCGCAAGCCTCGGTCTGAAAGTTAACGC,0.9870631,0.012936891,0
CTCGGTCTGAAAGTTAACGCCGGACACGGTCTGACCTATC,0.98490417,0.015095865,0
```


## Training the Network from Scratch:
- The model was built for training on a Linux environment, you may change line 115 and 116 to run on Windows - the '/' should be switched to '\\'.
- If you intend to rebuild the network from scratch, you may run the commands below - this is ideal for new training data.

		-- Import the Python Class.
			from PromotechCNN import PromotechCNN
			code = PromotechCNN()
		
		-- Setup the training data(Path to directory with training data is used).
			code.createTrainFastaFiles("/TrainingData/40nt-sequences/bacteria-1-10-ratio")
			code.buildData()

		-- Build and Train the network with 100 epochs, early stopping is in place so the network might stop before 100 epochs.
			code.buildBPNet(100)

		-- Predict the validation data. Used to build AUPRC and AUROC graphs
			code.predictValidation("/ValidationData/40nt-sequences/bacteria-1-1-ratio")

		-- Run prediction on some promoter sequence.
			code.predictSequenceFile("BACILLUS_negative.fasta")

