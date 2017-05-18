# Fig2code: create runnable, reproducible figures

Fig2code is a free, open-source tool to create figures that contain all the information needed to run them.  

## Motivation

A typical figure shared with a team, or embedded in a scientific manuscript is not backed up with the code, data and environment that created it. This can lead to wrong conclusions, stumbling productivity and knowledge being trapped on personal computers.  

Fig2code's goal is to make it easy for data scientists of all skill levels to create runnable, reproducible figures with minimal effort. 

Fig2code is based on Docker, but no knowledge of Docker is needed to use it. 

## When to use

Fig2code is best used when knowledge is being created, which might not include every figure produced. When a figure is created to support a conclusion, it should be backed up by the code, data and environment that created it. Fig2code is an easy way to create this supporting information, in a way that can be shared with the whole team, including technical and non-technical members. 

Fig2code is not meant as a replacement for the R and python packaging systems. If the primary purpose of your code is to be distributed as module to be re-used by others, please use the existing packaging systems. 

Fig2code supports R and R markdown. Support for python and jupyter is planed for the near future. 

## Usage

#### 1. Creating a runnable figure

```
fig2code run myScript.R 
```

This is going to do the following:

- examine  `myScript.R` file for dependencies and select the most appropriate base Docker container.
- run `myScript.R` inside Docker, isolate the code files that are necessary to run the figure and capture the outputs. 
- create an HTML version of the script output, e.g. If the script outputs `myFigure.png`, it will create `myFigure.html`. Meta information needed to re-run the figure, such as the Dockerfile, code and (smaller) data is recorded in hidden HTML tags. This meta information is designed to enlarge the file only minimally. Large files are recorded by their SHA256 value.

When opened in a browser the fig2code output HTML looks like a figure, but contains the full information need to re-run the code on any computer.

#### 2. Listing the content of a runnable figure

List the meta data store in the runnable figure:

```
fig2code list myFigure.html
```

This will return the list of meta data stored, e.g. code files, etc. 

See an individual files stored in the figure with:

```
fig2code cat myFigure.html myScript.R
```

This will show the content of the `myScript.R` file in the console. 

#### 3. Unpack a runnable figure

Unpack all the stored files into a separate directory:

```
fig2code unpack myFigure.html
```

This will create a directory `myFigure_src/` that will contain all the necessary files to re-run the figure. 

#### 4. See if any of the input files have changed
 
See if any of the files needed for the figure have changed:

```
fig2code diff myFigure.html
```

If the figure has been created by `myScript.R`, this command will check if the versions stored in `myFigure.html` and the current directory are the same. If not, it will show a diff file. 

#### 5. Test if the same figure is produced

Test if an identical figure will be produced by the current version of the script. 

```
fig2code test myScript.R
```

This will run `myScript.R` and compare the output, e.g. `myFigure.html` with the version of the file that was created before. If there are differences, this will be reported. 
 
#### 6. Examine the figure generation
 
```
fig2code examine myFigure.html
```

This will re-run the figure and start the R debugger at the end of the R script. You can examine the R workspace and run R commands. 

Start the R debugger at an arbitrary point in the R code:

```
fig2code examine -file myScript.R -line 1 myFigure.html
```
 
#### 7. Re-running a figure

```
fig2code run myFigure.html
```

This will use the meta information stored in `myFigure.html`, provision any necessary Docker containers and re-run the figure inside Docker. 

This is useful when the script generating the figure takes parameters:

```
fig2code run myFigure.html <argument 1> <argument 2>
```

or when the figure is based on Shiny, in which case a shiny server will be started and the user redirected to the browser.

If re-running the figure requires a large file, it will be located automatically using the fig2code SHA256 cache, that can be generated with:

```
fig2code add largeFile.rda
```

This adds `largeFile.rda` to the SHA256 cache, so it can be automatically found when needed to re-run a figure.  


## Configuration

Configure fig2code globally for the user by changing the `~/.fig2code.yml` file, which can be overriden by a `fig2code.yml` file in the current directory. 

## Licence

AGPL-3