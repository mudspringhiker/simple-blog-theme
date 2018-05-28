---
layout: post
title: "Scraping Data from a PDF File"
tags: ["parsing pdf", "RegEx", "Python"]
---

I've recently learned how to scrape data from PDF (portable data format) files. Here, I present how to do that in Python. I googled and found the [`pdfminer`](https://pypi.python.org/pypi/pdfminer3k/){:target="_blank"} module from the PyPI website. But the book, ["Web Scraping with Python" by Ryan Mitchell](http://shop.oreilly.com/product/0636920034391.do), which I happen to have, already has codes on how to do this. I only needed to install [`pdfminer`](https://pypi.python.org/pypi/pdfminer3k/).

### Installing the pdfminer module (Python)
##### Note: I'm using a Mac

There are a lot of pdfminer modules on PyPI but the one I chose works for Python 3, and has the `process_pdf` function which allowed me to run R. Mitchell's Python code.

- To start, I created a new folder that will contain my mini-project. 
- I downloaded the `tar` file from the [PyPI website](https://pypi.python.org/pypi/pdfminer3k/) and extracted it (by double-clicking the tar file). 
- I transferred the resulting folder, `pdfminer3k-1.3.1`, into the new folder I just created.
- Using the terminal/console, I `cd` in to the directory of the mini-project and created a virtual environment. I have Python 2 installed as my default Python version but to use Python 3, I used the following command to create the virtual environment:

    ```$ python3 -m venv venv```

- I activated the virtual environment by typing the following in the console:

    ```$ source venv/bin/activate```

    This changed my virtual environment to 'venv' (my arbitrary name for the virtual environment).
- Next was to install the module: I `cd` in to `pdfminer3k-1.3.1` folder and typed the following command:

    ```pdfminer3k-1.3.1 $ python setup.py install```

    Here is a screenshot of my console during installation. `parse_pdfminer3k` is the name of the folder for my mini-project.
![](/img/2017-09-07-parsingpdf/installingpdfminer3k.png)

    After installation, I obtained the following message:

    `Finished processing dependencies for pdfminer3k==1.3.1`.

To test whether the module works, the command line tool `pdf2txt.py` can be used (you need to be in the `pdfminer3k-1.3.1` folder):

```$ pdf2txt samples/simple1.py```

The following shows the result on the console:

![](/img/2017-09-07-parsingpdf/testing_pdfminer3k.png)

`pdf2txt.py` is actually a command line tool available when you install `pdfminer`. You can use it anytime in any folder that has the pdf file you want to parse. To know more how to use it, refer to the `index.html` file under the `docs` folder of `pdfminer`. Here is a [link](https://github.com/mudspringhiker/parsing_pdf_pdfminer3k/tree/master/pdfminer3k-1.3.1) to the `pdfminer3k.1.3.1` I used for my mini-project. 

One difficulty I encountered when installing this module is that `pdfminer` is actually available in many versions and the documentation for each of these versions are not very good. There is a 2014 version and a 2017 version. These versions work for using the command line tools, provided they are installed properly. Doing a `pip install pdfminer` I think uses the 2014 version. But the downloaded tar file from the link I provided above under a Python 3 environment works well in running the code I used in this mini-project.

### Extracting data from a PDF file

To illustrate extracting data from a PDF file, I want to get the "Meter Number" and the "Total Taxes & Fees on Electric Charges" from this [pdf](/img/2017-09-07-parsingpdf/sdge_bill.pdf) file. The desired string is found towards the bottom of the third page of the pdf file. The following shows the code to parse the pdf file:

{% highlight python %}# import modules, including the regex module, re
from pdfminer.pdfinterp import PDFResourceManager, process_pdf
from pdfminer.converter import TextConverter
from pdfminer.layout import LAParams
from io import StringIO
from io import open
import re

# define function to parse the PDF file
def readPDF(pdfFile):
    rsrcmgr = PDFResourceManager()
    retstr = StringIO()
    laparams = LAParams()
    device = TextConverter(rsrcmgr, retstr, laparams=laparams)
    process_pdf(rsrcmgr, device, pdfFile)
    device.close()
    content = retstr.getvalue()
    retstr.close()
    return content

# parse the pdf file
with open(pdf, "rb") as f:
    output = readPDF(f) 
{% endhighlight %}

With the data parsed, I can then extract the needed info using regular expressions.

{% highlight python %}meternumberregex = re.compile(r'(Meter Number: )\d{8}')
meternumber = meternumberregex.search(output).group()
print(meternumber)

taxesfeesregex = re.compile(r'(Total Taxes & Fees on Electric Charges  -)\s+\$\d\.\d+')
taxesfees = taxesfeesregex.search(output).group()
print(taxesfees)
{% endhighlight %}

Running this code results in the following:

{% highlight python %}Meter Number: 00613430
Total Taxes & Fees on Electric Charges  -         $1.41
{% endhighlight %}

The code for this mini-project is uploaded on [GitHub](https://github.com/mudspringhiker/parsing_pdf_pdfminer3k).


