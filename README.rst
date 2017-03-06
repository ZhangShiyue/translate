This code is derived from original seq2seq translation model on tensorflow(0.10.0).

Now, beam search has been added to decoding.

----------------
Train
----------------

Command line for training::

        python translate.py

Wmt10 and Wmt15 En-Fr translation data are used as train and dev set respectively. Data will be downloaded automatically
and saved in ./data/ by default.
During training, model will be saved every 200 minibatch updates in ./train/ by default. I choose to keep latest 1000
checkpoints, not overwrite (see self.saver in Seq2seqModel).

----------------
Test
----------------

Command line for testing::

        python ./translate.py --model translate.ckpt-397200 --decode < ./data/test.en > test_result.397200
        perl ./multi-bleu.perl data/devtest/test.fr < test_result.397200

I haved changed interactive decoding to test the BLUE on test set. Test set is also Wmt15, same as dev set,
but it is tokenized before used to test BLUE score.
Run above two command lines, I got BLUE = 6.66. I should say it is not a reasonable performance. I also tried interactive
decoding, which is also barely satisfactory::

        > Who is the president of the United States?
        Qui est le président des États-Unis ?
        > There are several people at the train station.
        Il y a plusieurs postes à la station .
        > Yesterday, I went to the mall to buy new clothes.
        _UNK , je _UNK à _UNK pour acheter des vêtements .
        > Tom had a dog named Jerry.
        Tom avait un _UNK _UNK .

Feel free to contact me by email (byryuer at gmail com) if you have any questions.