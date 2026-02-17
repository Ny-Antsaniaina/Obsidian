generate .pylintrc
pylint --generate-rcfile > .pylintrc

pylint --rcfile=.pylintrc fichier_bame.py
