# Set up Cloud9 with GitHub

1. Create a Cloud9 Environment

2. Create a GitHub Repository

   1. Initialize with README
   2. Add Python to .gitignore

3. In Cloud9, generate SSH-KEY for ssh cloning

   1. ```bash
      ssh-keygen -t rsa
      ```

   2. ```bash
      Copy the key to GitHub
      cat /home/ec2-user/.ssh/id_rsa.pub
      ```

4. In Github, Go to Settings / SSH and GPG Keys

   1. Create new ssh key and paste the key in.

5. Git clone the repo

6. Set up virtualenv 

   ```bash
   python3 -m venv ~/.projectname
   ```

7. Add into bashrc for convenient sourcing of projects

   ```bash
   vim ~/.bashrc
   Add into bashrc
   
   #alias
   alias hello="cd /home/ec2-user/environment/projectname && source ~/.projectname/bin/activate"
   
   #source bashrc
   source ~/.bashrc
   
   #check alias 
   alias
   ```

8. Type projectname and it will auto source into project venv.

9. Add a Makefile and convert Spaces to Tabs.

   ```makefile
   setup:
   	python3 -m venv ~/.projectname
   install:
   	pip install --upgrade pip &&\
   		pip install -r requirements.txt
   		
   test:
   	# python -m pytest -vv --cov=projectname tests/*.py
   	# python -m pytest --nbval notebook.ipynb
   	
   lint:
   	pylint --disable=R,C projectname
   	
   all: install lint test
   ```

10. Execute any make commands

    1. make setup
    2. make lint
    3. make lint
    4. make all

11. Setup CircleCI
    1. In CircleCI, setup projectname

    2. Copy sample config.yml 

    3. ```bash
       mkdir .circleci
       touch .circleci/config.yml
       ```

```yaml
#Example config
version: 2.1

orbs:
  python: circleci/python@0.2.1

jobs:
  build-and-test:
    executor: python/default
    steps:
      - checkout
      - python/load-cache
      - python/save-cache
      - run:
          name: install dependencies
          command: |
            python3 -m venv venv
            . venv/bin/activate
            make install
          
      - run:
          name: run lint
          command: |
            . venv/bin/activate
            make lint
          

workflows:
  main:
    jobs:
      - build-and-test

```









#### References

[How to setup C9](https://www.youtube.com/watch?time_continue=8&v=GpS_lnB6DVY&feature=emb_logo)

