```text
██████╗ ███████╗███████╗██████╗ ██╗    ██╗ ██████╗ ██████╗ ██████╗
██╔══██╗██╔════╝██╔════╝██╔══██╗██║    ██║██╔═══██╗██╔══██╗██╔══██╗
██║  ██║█████╗  █████╗  ██████╔╝██║ █╗ ██║██║   ██║██████╔╝██║  ██║
██║  ██║██╔══╝  ██╔══╝  ██╔═══╝ ██║███╗██║██║   ██║██╔══██╗██║  ██║
██████╔╝███████╗███████╗██║     ╚███╔███╔╝╚██████╔╝██║  ██║██████╔╝
╚═════╝ ╚══════╝╚══════╝╚═╝      ╚══╝╚══╝  ╚═════╝ ╚═╝  ╚═╝╚═════╝ 
```

# Learning to Generalize for Sequential Decision Making

## Abstract and Background
> We consider problems of making sequences of decisions to accomplish tasks,  interacting via the medium of language. These problems are often tackled with reinforcement learning approaches. We find that these models do not generalize well when applied to novel task domains. However, the large amount of computation necessary to adequately train and explore the search space of sequential decision making, under a reinforcement learning paradigm, precludes the inclusion of large contextualized language models, which might otherwise enable the desired generalization ability. We introduce a teacher-student imitation learning methodology and a means of converting a reinforcement learning model into a natural language understanding model. Together, these methodologies enable the introduction of contextualized language models into the sequential decision making problem space. We show that models can learn faster and generalize more, leveraging both the imitation learning and the reformulation. Our models exceed teacher performance on various held-out decision problems, by up to 7\% on in-domain problems and 24\% on out-of-domain problems. 

Sequential decision-making tasks that are mostly solved by reinforcement learning for video games have been tackled for years since Minh et al. 2013. There is more and more attention to text-based ones in recent years ( e.g., Ammanabrolu and Mark Riedl, 2019 NAACL; Yuan et al., 2019 EMNLP;  Adolphs and Hofmann, 2020 AAAI; Jain et al., 2020 AAAI; Madotto et al., 2020 IJCAI).  However, the apparent inclusion of large contextualized language models, e.g., BERT, is precluded for a large amount of computation to train a reinforcement learning model adequately. We solve the problem by introducing a Teacher-Student training framework to incorporate the BERT model. We show that, by evaluating both in-domain and out-of-domain tasks, the sequential decision-making problems benefit from the commonsense knowledge embedded in the BERT model, and the generalization ability of agents are being improved even without a more extensive exploration of task spaces.

## Cite our paper

```
TBD
```

## Experiments

- Code repository: https://github.com/yinxusen/deepword
- Setup the Deepword with the [tutorial](https://github.com/yinxusen/deepword/blob/master/tutorial.md).

Suppose the directory of the Deepword is `$PDIR`.

Suppose the directory that contains this file is `$CDIR`.

### Sample data and games

We provide sample data and games in the repository. Notice that our raw data and games are up to 10GB, so we suggest users generate their training data with the Deepword repository.

We will figure out how to share all data and games later. Before that, you can request full data and games through email (yinxusen AT gmail DOT com).


### Pre-defined hyperparameters

The hyperparameters used for training the student models are stored in `$CDIR/student-model-configs`.


### Train DRRN-CNN student model


```bash
MODELHOME="$HOME/cnn-student-model"
DATAPATH="$CDIR/sample-data"

pushd $PDIR
./sbin/run.sh python/deepword/main.py \
    --config-file model_config/drrn-cnn-legacy-student.yaml \
    --model-dir "$MODELHOME" \
    "train-student" \
    --data-path "$DATAPATH" --n-epochs 100
popd
```

### Train DRRN-CNN-Glove student model

```bash
MODELHOME="$HOME/cnn-glove-student-model"
DATAPATH="$CDIR/sample-data"

pushd $PDIR
./sbin/run.sh python/deepword/main.py \
    --config-file model_config/drrn-cnn-glove.yaml \
    --model-dir "$MODELHOME" \
    "train-student" \
    --data-path "$DATAPATH" --n-epochs 100
popd
```

### Train DRRN-Bert student model

```bash
MODELHOME="$HOME/drrn-bert-student-model"
DATAPATH="$CDIR/sample-data"

pushd $PDIR
./sbin/run.sh python/deepword/main.py \
    --config-file model_config/drrn-bert.yaml \
    --model-dir "$MODELHOME" \
    "train-student" \
    --data-path "$DATAPATH" --n-epochs 100
popd
```

### Train DRRN-Transformer student model

Same with the above one, except that do not initialize the Bert model with pre-trained weights.

I'll add args to do this later.

### Train Commonsense-Bert student model

```bash
MODELHOME="$HOME/commonsense-bert-student-model"
DATAPATH="$CDIR/sample-data"

pushd $PDIR
./sbin/run.sh python/deepword/main.py \
    --config-file model_config/commonsense-bert.yaml \
    --model-dir "$MODELHOME" \
    "train-student" \
    --data-path "$DATAPATH" --n-epochs 100
popd
```

### Train Commonsense-Bert student model with softmax loss

```bash
MODELHOME="$HOME/commonsense-bert-softmax-student-model"
DATAPATH="$CDIR/sample-data"

pushd $PDIR
./sbin/run.sh python/deepword/main.py \
    --config-file model_config/commonsense-softmax-bert.yaml \
    --model-dir "$MODELHOME" \
    "train-student" \
    --data-path "$DATAPATH" --n-epochs 100
popd
```

### Evaluate student models

Let's take the example of the DRRN-CNN model:

```bash
MODELHOME="$HOME/cnn-student-model"
GAMEPATH="$CDIR/sample-games"


pushd "$PDIR"
./sbin/run.sh python/deepword/main.py \
    --agent-clazz "CompetitionAgent" \
    --eval-episode 2 \
    --model-dir "$MODELHOME" \
    "eval-dqn" \
    --eval-mode "full-eval" --game-path "$GAMEPATH" --debug
popd
```

### Other training

Try

```bash
./sbin/run.sh python/deepword/main.py -h
usage: main.py [-h] [--model-creator MODEL_CREATOR]
               [--agent-clazz AGENT_CLAZZ] [--config-file CONFIG_FILE]
               [--init-eps INIT_EPS] [--final-eps FINAL_EPS]
               [--annealing-eps-t ANNEALING_EPS_T] [--gamma GAMMA]
               [--eval-episode EVAL_EPISODE] [--batch-size BATCH_SIZE]
               [--learning-rate LEARNING_RATE] [--save-gap-t SAVE_GAP_T]
               [--replay-mem REPLAY_MEM] [--observation-t OBSERVATION_T]
               [--total-t TOTAL_T]
               [--game-episode-terminal-t GAME_EPISODE_TERMINAL_T]
               [--collect-floor-plan] [--disable-collect-floor-plan]
               [--start-t-ignore-model-t] [--n-actions N_ACTIONS]
               [--use-step-wise-reward] [--always-compute-policy]
               [--tokenizer-type TOKENIZER_TYPE]
               [--max-snapshot-to-keep MAX_SNAPSHOT_TO_KEEP]
               [--policy-to-action POLICY_TO_ACTION]
               [--policy-sampling-temp POLICY_SAMPLING_TEMP]
               [--action-file ACTION_FILE] [--policy-eps POLICY_EPS]
               --model-dir MODEL_DIR
               {train-dqn,eval-dqn,train-student,eval-student,gen-data} ...

positional arguments:
  {train-dqn,eval-dqn,train-student,eval-student,gen-data}
                        [train-dqn|eva--dqn|train-student|eval-student|gen-
                        data]

optional arguments:
  -h, --help            show this help message and exit
  --model-creator MODEL_CREATOR
  --agent-clazz AGENT_CLAZZ
  --config-file CONFIG_FILE
  --init-eps INIT_EPS
  --final-eps FINAL_EPS
  --annealing-eps-t ANNEALING_EPS_T
  --gamma GAMMA
  --eval-episode EVAL_EPISODE
  --batch-size BATCH_SIZE
  --learning-rate LEARNING_RATE
  --save-gap-t SAVE_GAP_T
  --replay-mem REPLAY_MEM
  --observation-t OBSERVATION_T
  --total-t TOTAL_T
  --game-episode-terminal-t GAME_EPISODE_TERMINAL_T
  --collect-floor-plan
  --disable-collect-floor-plan
  --start-t-ignore-model-t
  --n-actions N_ACTIONS
  --use-step-wise-reward
  --always-compute-policy
  --tokenizer-type TOKENIZER_TYPE
                        [bert|albert|nltk]
  --max-snapshot-to-keep MAX_SNAPSHOT_TO_KEEP
  --policy-to-action POLICY_TO_ACTION
                        [EPS/LinUCB/Sampling]
  --policy-sampling-temp POLICY_SAMPLING_TEMP
  --action-file ACTION_FILE
  --policy-eps POLICY_EPS
  --model-dir MODEL_DIR
```

and

```bash
./sbin/run.sh python/deepword/main.py train-student -h
usage: main.py train-student [-h] --data-path DATA_PATH
                             [--learner-clazz LEARNER_CLAZZ]
                             [--n-epochs N_EPOCHS]

optional arguments:
  -h, --help            show this help message and exit
  --data-path DATA_PATH
  --learner-clazz LEARNER_CLAZZ
  --n-epochs N_EPOCHS
```

```bash
./sbin/run.sh python/deepword/main.py eval-student -h
usage: main.py eval-student [-h] --data-path DATA_PATH
                            [--learner-clazz LEARNER_CLAZZ] [--n-gpus N_GPUS]

optional arguments:
  -h, --help            show this help message and exit
  --data-path DATA_PATH
  --learner-clazz LEARNER_CLAZZ
  --n-gpus N_GPUS
```
