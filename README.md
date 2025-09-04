An LLM-Powered Agent that uses a ReAct behviour to analyze and optimize solutions to ML workflows.

# Architecture
The system uses a ML_Agent class that maintains conversation state and can call external tools. It's built on top of the Groq API using the Llama 3.3 70B model.

# ReAct Pattern Implementation: 

The agent follows a "Thought-Action-Observation" loop where it:

1. Reasons about what to do next (Thought)
2. Takes an action using a tool (Action)
3. Receives feedback from the environment (Observation)
4. Continues until the task is complete

# Available Tools
The agent has 6 specialized tools for ML operations:

model_memory - Acts as a knowledge base for datasets (Iris, Penguins, CIFAR-10) and models (Logistic Regression, Decision Tree, KNN)
dataset_loader - Loads datasets using sklearn and OpenML
dataset_preprocessing - Handles data splitting, scaling, encoding, and missing value imputation
train_model - Trains models with appropriate dataset/model combinations
evaluate_model - Calculates performance metrics (accuracy, precision, recall, F1)
visualize_results - Generates plots like confusion matrices and feature importance

# Execution Flow: 
- The query() function implements the main automation loop:
```pythonACTION_PATTERN = re.compile("^Action: (\\w+): (.*)$")```
- This regex pattern parses the LLM's responses to extract tool calls. When the agent decides to use a tool, it formats its response as "Action: tool_name: input", which gets:

* Parsed by the regex
* Mapped to the actual Python function via KNOWN_ACTIONS
* Executed with the provided input
* Results fed back as "Observation: ..." for the next iteration

# System Prompt Design
The agent is guided by a detailed system prompt that:

- Defines available tools and their purposes
- Establishes workflow guidelines (load → preprocess → train → evaluate)
- Specifies the Thought-Action-Observation format
- Includes compatibility rules (e.g., don't train without preprocessing)

# Example Workflow:
When asked to "Train logistic regression on Iris dataset", the agent:

* Thinks: "I need to load the Iris dataset first"
* Acts: dataset_loader: iris
* Observes: Dataset loaded with 150 samples, 4 features
* Thinks: "Now I should preprocess the data"
* Acts: dataset_preprocessing: iris
Observes: Data split 80/20, features standardized
Continues through training, evaluation, and visualization
