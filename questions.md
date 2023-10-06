# Questions from Johannes Kirschner

I just wanted to add a few points/questions around compute at SDSC that I am facing right now. Perhaps you can consider addressing some of them in the meeting, if they are relevant more broadly. Some might be quite obvious to the expert user, but I am still learning in particular about the best use of docker.

* CSCS vs RunAI vs renkulab: Which one is the suited for which tasks? Can we switch between CSCS and runai based on needs, also during the projects?
* In the past, I worked with virtual machines and euler/sbatch and my workflow seems to be most efficient with sbatch type of submission systems. This is for running hyperparameter searches or benchmarking families of algorithms, often with different hyperparameters. I am still not sure if RunAI is ideal for this workflow.
* Integrating remote compute directly into the IDE (pycharm). So far I didn't really manage to find a useful configuration there. Instead I sync my files, and then start the jobs on ssh or with a short bash script. I'd be curious to hear about other workflows used in practice.
* Docker images: Dockerhub vs Harbour vs Gitlab registry: What's the best/most convenient place to store images?
* Docker images: What are common workflows? I.e. one docker image per project with customisation, or do the customisation just based on the requirements.txt? How often do I need to rebuild? Is it better to include the src files in the image vs mounting persistent storage vs cloning the git?
* Docker images: Is the image size and startup time of the container a concern? E.g. I often need to evaluate an algorithms that runs for a few minutes, with 100 different configurations and I want to run each version 20 (or more) times as it is interacting with a stochastic environment. On sbatch I would just submit 20*100 jobs and be done. With runai, if each job needs to download a few GBs for the docker image and spin up the container, this seems to be a bit wasteful and potentially slower compared to other workflows (I am not sure). The requirements are a bit different compared to the "start once and train for a week".
* One last point that bothers me is: Why install conda (or venv) inside docker? I.e. why have a container in a container? Conda is made for managing different python environments, but the docker container typically is used only for one project So why don't we instead install python directly via the system's package manager, and then do pip install -r requirements?