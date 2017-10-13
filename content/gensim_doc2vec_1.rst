Troubleshooting the gensim implementation of Doc2Vec
##########################################################

:date: 2017-01-25 02:40
:modified: 2017-01-25 02:40
:tags: gensim, word2vec, machine learning 
:category: natural language processing
:slug: about-gensim-and-doc2vec
:authors: Melvyn Drag
:summary: A Small Post About Open Source Software

This adventure started when I was experimenting with the (wonderful) implementation of Doc2Vec in the `gensim <https://github.com/RaRe-Technologies/gensim/>`_ package.


I was experimenting with Doc2Vec to get a handle on it, and wrote the following code, which kept throwing an error.

.. code-block:: python

    import gensim
    import multiprocessing
    
    dirname="/home/temp/nltk_data/corpora/brown"    
    documents = gensim.models.doc2vec.TaggedBrownCorpus(dirname)
    cores = multiprocessing.cpu_count()
    model = gensim.models.Doc2Vec(dm=0, dbow_words=1, size=200, window=8, min_count=19, iter=10, workers=cores)
    model.build_vocab(documents)
    model.train(documents)
    
    # I'll explain the doc_of_interest in a minute.
    doc_of_interest = "/home/temp/nltk_data/corpora/brown/ca01_SENT_0" 
    like_doc_of_interest = model.docvecs.most_similar(positive=[doc_id], topn=5)
    print(like_doc_of_interest)

And this code was throwing the following error. 
    
::
    
    /home/temp/anaconda3/lib/python3.5/site-packages/gensim/models/doc2vec.py", line 448, in most_similar
    elif doc in self.doctags or doc < self.count:
    TypeError: unorderable types: str() < int()

At this point I knew we were in for the standard procedure of working on code in a linux environment: Find the source code, read it carefully for  a few hours, scratch our heads, swear a bit, and then realize what hair-brained thing is going on before proceeding to fix it. I had been playing iwith doc2vec earlier in the day and had already seen this error and intuited that it meant the the doc_id you are passing to the most_similar function doesn't exist. I didn't know how the TaggedBrownCorpus class worked, but in the `source <https://github.com/RaRe-Technologies/gensim/blob/develop/gensim/models/doc2vec.py>`_ it said that it takes a dirname parameter - I correctly guessed that they meant the location of the brown corpus in your nltk download. This was strange to me because in the NLTK package if you

.. code-block:: python

    from nltk.corpus import brown

and you haven't yet installed the corpus with the nltk.download() command, it spits out an error saying that it can't find the corpus, and then spits out a list of standard search paths that it has checked. But gensim didn't do that so that seemed weird - this is an idea for a simple pull request for someone to work on and send.

Anyway, I looked at the TaggedBrownCorpus class in the source

.. code-block:: python

    class TaggedBrownCorpus(object):
        """Iterate over documents from the Brown corpus (part of NLTK data), yielding
        each document out as a TaggedDocument object."""
        def __init__(self, dirname):
            self.dirname = dirname
    
        def __iter__(self):
            for fname in os.listdir(self.dirname):
                fname = os.path.join(self.dirname, fname)
                if not os.path.isfile(fname):
                    continue
                for item_no, line in enumerate(utils.smart_open(fname)):
                    line = utils.to_unicode(line)
                    # each file line is a single document in the Brown corpus
                    # each token is WORD/POS_TAG
                    token_tags = [t.split('/') for t in line.split() if len(t.split('/')) == 2]
                    # ignore words with non-alphabetic tags like ",", "!" etc (punctuation, weird stuff)
                    words = ["%s/%s" % (token.lower(), tag[:2]) for token, tag in token_tags if tag[:2].isalpha()]
                    if not words:  # don't bother sending out empty documents
                        continue
                    yield TaggedDocument(words, ['%s_SENT_%s' % (fname, item_no)])

It's hard to see this in the documentation, but the iterator given by the class gives TaggedDocuments that consist of a list of words and a list of labels. In this class, the labels are:

.. code-block:: python
    
    ['%s_SENT_%s' % (fname, item_no)]

So I just thought I'd look at the first sentence and see what it was similar to. It seemed that the first sentence would be labelled

.. code-block:: python 

    doc_id = "/home/temp/nltk_data/corpora/brown/ca01_SENT_0"
 
Because when I did an ls or ll in "/home/temp/nltk_data/corpora/brown/", ca01 was the first file listed. And the item_no in the for loop above would start at 0. *(If you don't already love it, look into the enumerate() function in python, it is very useful and idiomatic.)* Long story short, the first document was ca01_SENT_2. I figured this out with the following code:

.. code-block:: python

    import gensim
    import multiprocessing
    
    dirname="/home/temp/nltk_data/corpora/brown"
    
    documents = gensim.models.doc2vec.TaggedBrownCorpus(dirname)
    
    all_docs = list(documents)
    all_tags = [tagged_document.tags[0] for tagged_document in all_docs]
    all_tags = [at for at in all_tags if 'CONTENT' not in at] 
    all_tags = [at for at in all_tags if 'ca01' in at] 
    all_tags.sort()
    print(all_tags)

Then, I was really perplexed, so I went to look at the ca01 file. As it turns out, the lines in these files are separated by two blank lines. So, the first non-empty line is not line0, not line 1, but line 2. Then line 5, 8, and so on, skipping two lines in between.

The moral of the story: The age-old adage: "Garbage in, garbage out". In our profession you have to be vary careful about particulars. As a simple project, someone needs to improve the error handling in Doc2Vec, because the error I showed above was in no way helpful. After I sorted out this issue I went on to do lots of interesting experiments, and we should be grateful to the developers who made this software freely available for both academic and commercial use.
