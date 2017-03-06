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
but it should be tokenized before used to test BLUE score. The beam_size of beam search is 5 by default.

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

To figure out the reason of poor performance, I inspected the "alignments" of attention model. And I found a bug in original
code. I found the alignments are almost same through out a sentence, which is contradict to the goal of attention, to find
related source words to attend. I also found this problem on BTEC Zh-En data. I believe this is a common problem no matter
which data you use.

So, I examined 'seq2seq.py' code carefully, and found the original initialization of 'AttnV' variable is quite improper.
'AttnV' is randomly initialized from [-sqrt(3), +sqrt(3)] uniform distribution, so initial value is so large that makes
initial alignments approximate to a one-hot vector and thus the gradients on 'AttenV', 'AttnW' are rather small. Therefore,
the alignment model cannot be trained.

I solve this problem by constantly initializing 'AttnV' to 0.

This change improves the performance on BTEC Zh-En corpus by more than 3 blue score (from 43.0 to 46.71).
I'm testing it on En-Fr data...

Feel free to contact me by email (byryuer at gmail com) if you have any questions.