This is an example of a cookiecutter template for dynamic document generation. I use this in my own work for cutting legal agreements which need various dates and names filled in throughout the template. Here I made just a simple letter fill-in as an example. The real power comes when you make decisions in the template based on yaml key/value pairs and their values. So for more on that read the [jinja2 docs](http://jinja.pocoo.org/docs/2.9/).

## Demo

First we create a jinja2 template and apply values using pandoc to a LibreOffice template (which just holds styles, no content or template variables).

An example jinja2 template.
```
{{salutation}} {{ to_name }},


### Update your records

Our records indicate your address is:

{{ to_address }}


{% if renewal %}
### Time for renewal

We also see that you'll be up for renewal on {{ renew_date }}. Would you like to renew now? Contact us at 555-2323
{% endif %}

{{closing}},
{{from_name}}
```

Cookiecutter can pull the repo down from github and then prompt for the values we want for each key interactively.
```
$ cookiecutter https://github.com/jduckles/ccdocs
You've cloned /Users/jduckles/.cookiecutters/ccdocs before. Is it okay to delete and re-clone it? [yes]: yes
dirname [directory_name]: myletter
to_name [John Smith]:
to_address [123 Infinite Court Majestic, CA 99999]:
to_phonenum [+1 555 1212]:
salutation [Dear]:
closing [Regards]:
from_name [Michael Miller]:
renewal [True]:
renew_date [March 12, 2018]:

cd myletter
```

In this directory we now have a yaml file with pre-filled key/value pairs that will be filled in the document. If we made a mistake above, we can edit this file and the changes will propogate the next time you run `make`
```
to_name: John Smith
to_address: 123 Infinite Court Majestic, CA 99999
to_phonenum: +1 555 1212
salutation: Dear
closing: Regards
from_name: Michael Miller
renewal: True
renew_date: March 12, 2018
```

Run make to build the document.
```
make

jinja2 --format yaml template.j2 myletter.yaml > myletter.md
pandoc --reference-odt odt/template.odt -o myletter.odt myletter.md
/Applications/LibreOffice.app/Contents/MacOS/soffice -env:UserInstallation="file:///tmp/LibO_Conversion" --headless --invisible --convert-to pdf myletter.odt
convert /private/tmp/myletter/myletter.odt -> /private/tmp/myletter/myletter.pdf using filter : writer_pdf_Export
rm myletter.md
```

![](https://jduckles-dropshare.s3-us-west-2.amazonaws.com/Screen-Shot-2017-04-27-11-53-50-7WagERkTtw.png)

You can edit the LibreOffice template and set default fonts and styles in the template and they will be applied through to the PDF. You can even generate letterhead by adding a logo. Note that the template can be empty, but the styles for *Body Text*, *H1*, *H2* etc should be configured in the template.odt file to suit your needs.

## Requirements
Basic setup and makefile for using python cookiecutter to build templated documents.

This requirements are: `jinja2, jinja2-cli, pandoc, LibreOffice`

```
pip3 install jinja2 jinja2-cli
brew install pandoc
brew cask install libreoffice
```

## Setting paths
You'll need to edit the Makefile in `{{cookiecutter.dirname}}` in order to point to the appropriate path for LibreOffice's soffice executable. No changes should be necessary on a Mac if you've installed LibreOffice in `/Applications`.

## Configuring your own template

1. Fork/copy this repo to your own account
2. Edit `template.j2` for your needs, adding `{{ keyname }}` with unique keys for where you want fillins.
3. Set key/value pairs in both `cookiecutter.json` and `{{cookiecutter.dirname}}/{{cookiecutter.dirname}}.yaml`
4. Give it a go!

