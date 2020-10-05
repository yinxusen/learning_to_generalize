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

Suppose the directory that contains this file is `CDIR`.

### Train CNN student model


```bash
MODELHOME="$HOME/cnn-student-model"
DATAPATH="$CDIR/sample-data"

pushd $PDIR
./sbin/run.sh python/deeptextworld/main.py \
    --config-file model_config/drrn-cnn-legacy-student.yaml \
    --model-dir "$MODELHOME" \
    "train-student" \
    --data-path "$DATAPATH" --n-epochs 100
popd
```

### Evaluate CNN student model

```bash
MODELHOME="$HOME/cnn-student-model"
GAMEPATH="$CDIR/sample-games"


pushd "$PDIR"
./sbin/run.sh python/deeptextworld/main.py \
    --agent-clazz "CompetitionAgent" \
    --eval-episode 2 \
    --model-dir "$MODELHOME" \
    "eval-dqn" \
    --eval-mode "full-eval" --game-path "$GAMEPATH" --debug
popd
```

