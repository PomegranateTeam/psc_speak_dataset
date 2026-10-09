## Introduction

**PSC-Speak-1500** is a Mandarin speech dataset built around the **proposition-speaking (命题说话)** component of the Putonghua Proficiency Test (PSC). In this task, a speaker gives an approximately three-minute spontaneous response to a fixed topic without reading a script. Recordings are evaluated for pronunciation standardness, lexical and grammatical conformity, naturalness and fluency, together with additional rule-based deductions.

The complete dataset comprises **1,500 recordings** from **866 adult speakers**, covering **77 fixed speaking topics** and **74.13 hours of audio**. Each recording has an individually corrected Mandarin transcript and independent scores from three credentialed PSC raters. The dataset also provides consensus scores, speaker and topic metadata, and temporal annotations.

**Current repository release:** All five JSON files contain the full dataset records and split information. To illustrate the audio and alignment formats, the repository includes **9 WAV files and 9 corresponding Praat TextGrid files only**: **3 pairs from the training split, 3 from the validation split, and 3 from the test split**. The other recordings and TextGrid files are not included in this repository.

### Dataset Overview

| Item | Description |
| --- | --- |
| Speaking task | PSC proposition speaking; approximately 3 minutes per response |
| Language | Mandarin Chinese |
| Speakers | 866 adults, aged 18–49 |
| Recordings | 1,500 |
| Fixed topics | 77 |
| Total audio duration (complete dataset) | 74.13 hours |
| Audio specification | 16-kHz, 16-bit, mono WAV |
| Expert annotations | 3 independent PSC raters per recording |
| Other annotations | Corrected transcripts, consensus deductions, final scores, timestamps and TextGrid alignment |
| Data splits | Train / validation (`dev`) / test, with no speaker overlap |

### Split and Repository Availability

| Split | Records in JSON | WAV examples included | TextGrid examples included |
| --- | ---: | ---: | ---: |
| Train | 700 | 3 | 3 |
| Validation (`dev`) | 200 | 3 | 3 |
| Test | 600 | 3 | 3 |
| **Total** | **1,500** | **9** | **9** |

The split JSON files are complete; the limited number of example audio and TextGrid files **does not** indicate missing JSON records. An `audio_file_path` recorded in JSON may refer to audio that is not distributed in this repository.

## Scoring Criteria

PSC proposition speaking is assessed on a **40-point scale**. Three core dimensions account for the 40 base points, while four additional dimensions record deductions for specific conditions.

| Dimension | JSON field | Base score | Maximum deduction |
| --- | --- | ---: | ---: |
| Pronunciation standardness | `audiolevel` | 25 | 14 |
| Lexical and grammatical conformity | `lexicalgrammar` | 10 | 4 |
| Naturalness and fluency | `naturalfluency` | 5 | 3 |
| Insufficient speaking duration | `lackoftime` | — | 6 |
| Off-topic or duplicated content | `digress` | — | 6 |
| Invalid speech | `invalidcorpus` | — | 6 |
| Memorized or read-script delivery | `readthetext` | — | 1 |

The principal scoring bands are:

- **Pronunciation standardness:** deductions of 0–2, 3–4, 5–6, 7–8, 9–11, or 12–14 points, corresponding to six increasing levels of pronunciation difficulty.
- **Lexical and grammatical conformity:** 0, 1–2, or 3–4 deducted points, from standard usage to frequent non-standard usage.
- **Naturalness and fluency:** 0, 0.5–1, or 2–3 deducted points, from natural fluent speech to substantially disjointed speech.

Each of the three raters scores a recording independently. For every dimension, the consensus deduction uses the value agreed upon by at least two raters; if all three values differ, it uses their arithmetic mean. The general score calculation is:

**Final score = 40 − sum of the seven consensus deductions.**

In the JSON data, `point` is the number of deducted points and `label` is the stored rating-band identifier. These fields are not interchangeable.

## Repository Structure

```text
psc_speak_dataset/
├── README.md
├── psc_score.json
├── psc_score_train.json
├── psc_score_dev.json
├── psc_score_test.json
├── psc_score_details.json
├── audios/                 # 9 example WAV files
└── textgrids/              # 9 matching TextGrid files
```

The included files serve the following purposes:

| File or directory | Content |
| --- | --- |
| `psc_score.json` | Consensus scores and de-identified metadata for all 1,500 recordings |
| `psc_score_train.json` | All 700 training records |
| `psc_score_dev.json` | All 200 validation records |
| `psc_score_test.json` | All 600 test records |
| `psc_score_details.json` | Detailed records, including three raters' deductions, consensus scores, and timestamp annotations |
| `audios/` | 9 WAV examples, with 3 selected from each split |
| `textgrids/` | 9 matching Praat TextGrid examples, with 3 selected from each split |

### JSON Fields

| Field | Meaning |
| --- | --- |
| `speaker_id` | De-identified speaker identifier |
| `text` | Manually corrected Mandarin transcript |
| `audio_file_path` | Audio path stored in the record; the file may not be included in this repository |
| `topic`, `topic_2` | Speaking topic and secondary/normalized topic |
| `level` | PSC proficiency-level label |
| `experts.expert1`, `experts.expert2`, `experts.expert3` | Independent expert annotations |
| `experts.*.deduct_points` | Seven scoring dimensions, each with `point` and `label` |
| `experts.consensus_avg.deduct_points` | Fused deductions from the three experts |
| `experts.consensus_avg.total_score` | Final score on the 40-point scale |
| `timestamp.silence` | Recording-level silence intervals |
| `timestamp.noise` | Noise or invalid-speech intervals |
| `timestamp.sentence` | Sentence-level segments, optionally with transcript `text` and nested `items` |
| `start`, `end`, `tag` | Interval start/end times (seconds) and segment type |

### Actual JSON Record Example

The following example uses **actual values from a de-identified PSC-Speak-1500 record**, including the corrected transcript, expert deductions, consensus score, and representative timestamp segments. **Only intermediate timestamp-array entries have been omitted** for readability; this is an excerpt, not a complete replacement for the corresponding JSON record.

```json
{
  "speaker_id": "spk00294",
  "text": "我最喜爱的职业。从小时候开始我就特别喜欢当律师。每次我们的语文老师问我们，你长大以后要当想当什么的时候我第一个说出来的就是我当律师。但是走到自己的职业生涯中我选择了法官则个职业。我特别喜欢我的这个职业。从二零二零年开始我就进入到法院的行业中，从事着法官的职业。从一个小小的书记员到法官助理然后到法官，从法官到厅长当副院长然后院长。我走过的每一步，每一段的工作经历都给了我很大的鼓励和勇气。第一次记得第一次在审理一件民事案件的时候，特别紧张，也特别恐惧，想的能不能办好，能不能解决好则个纠纷，能不能给当事人给一个公道，我心里面想的特别多特别多。但是第一起案件我人生经历上的第一个案件，我就调解处理了。调解完了以后我看着那个当事人泪汪汪的看着我说，谢谢你法官，谢谢你，谢谢你党和政府的时候，我想到了我没有选错我的职业，我喜欢我的职业，喜欢我的职业不仅仅是我爱则个职业，因为我继承了我爸爸的职业，因为我爸爸就是法官，就是院长，我在跟着他走。自从二零一四年爸爸去世以后，我就告四自己，一定要跟着他的步伐走，一邓要继承他的职业，做好自己的工作让一个每一给当事人在每一个司法案件中感受到公平和正义。总的来说我很喜欢我的法官职业，我很喜欢为当事人排忧解难，我很喜欢为当事人解决矛盾纠纷，把党和政府的温暖送到每一个需要帮助的当事人手中。所以我爱我的职业，爱法官则个职业。",
  "audio_file_path": "psc_speak_dataset/audios/PSC_SPK0394.wav",
  "topic": "我喜欢的职业(或专业)",
  "topic_2": "我喜欢的职业",
  "level": "二级甲等",
  "experts": {
    "expert1": {
      "deduct_points": {
        "digress": {"point": 0.0, "label": 1},
        "lackoftime": {"point": 0.0, "label": 1},
        "audiolevel": {"point": 6.0, "label": 3},
        "readthetext": {"point": 0.0, "label": 1},
        "invalidcorpus": {"point": 0.0, "label": 1},
        "lexicalgrammar": {"point": 0.0, "label": 1},
        "naturalfluency": {"point": 0.0, "label": 1}
      }
    },
    "expert2": {
      "deduct_points": {
        "digress": {"point": 0.0, "label": 1},
        "lackoftime": {"point": 0.0, "label": 1},
        "audiolevel": {"point": 5.5, "label": 3},
        "readthetext": {"point": 0.0, "label": 1},
        "invalidcorpus": {"point": 0.0, "label": 1},
        "lexicalgrammar": {"point": 0.0, "label": 1},
        "naturalfluency": {"point": 0.0, "label": 1}
      }
    },
    "expert3": {
      "deduct_points": {
        "digress": {"point": 0.0, "label": 1},
        "lackoftime": {"point": 0.0, "label": 1},
        "audiolevel": {"point": 6.0, "label": 3},
        "readthetext": {"point": 0.0, "label": 1},
        "invalidcorpus": {"point": 0.0, "label": 1},
        "lexicalgrammar": {"point": 0.0, "label": 1},
        "naturalfluency": {"point": 0.0, "label": 1}
      }
    },
    "consensus_avg": {
      "deduct_points": {
        "digress": {"point": 0.0, "label": 1},
        "lackoftime": {"point": 0.0, "label": 1},
        "audiolevel": {"point": 6.0, "label": 3},
        "readthetext": {"point": 0.0, "label": 1},
        "invalidcorpus": {"point": 0.0, "label": 1},
        "lexicalgrammar": {"point": 0.0, "label": 1},
        "naturalfluency": {"point": 0.0, "label": 1}
      },
      "total_score": 34.0
    }
  },
  "timestamp": {
    "silence": [
      {
        "start": 0.0,
        "end": 1.2781164807313723,
        "tag": "sil"
      },
      ...
      {
        "start": 180.03,
        "end": 180.16,
        "tag": "sil"
      }
    ],
    "noise": [
      {
        "start": 19.13260689239385,
        "end": 19.886496072764132,
        "tag": "noise"
      },
      ...
      {
        "start": 154.49230876648403,
        "end": 154.89463957088924,
        "tag": "noise"
      }
    ],
    "sentence": [
      {
        "start": 1.2781164807313723,
        "end": 3.1877641899547386,
        "tag": "speech",
        "text": "我最喜爱的职业。",
        "items": [
          {
            "start": 1.2781164807313723,
            "end": 3.1877641899547386,
            "tag": "speech"
          }
        ]
      },
      ...
      {
        "start": 175.97455775476507,
        "end": 180.03,
        "tag": "speech",
        "text": "所以我爱我的职业，爱法官则个职业。",
        "items": [
          {
            "start": 175.97455775476507,
            "end": 177.74191800240735,
            "tag": "speech"
          },
          {
            "start": 177.74191800240735,
            "end": 178.4340246270727,
            "tag": "sil"
          },
          {
            "start": 178.4340246270727,
            "end": 180.03,
            "tag": "speech"
          }
        ]
      }
    ]
  }
}
```

The three pronunciation deductions for this record are **6.0**, **5.5**, and **6.0**; the consensus pronunciation deduction is **6.0**, giving a final score of **34.0** because the remaining deductions are zero. This example record is drawn from the complete metadata and is **not necessarily among the nine audio/TextGrid examples** supplied here.

## Audio and TextGrid Annotations

The audio is standardized as mono WAV at 16 kHz and 16-bit resolution. Transcripts are manually corrected and retain naturally occurring hesitations, repetitions, and self-repairs.

The accompanying Praat TextGrid annotation format uses three aligned tiers:

| Tier | Annotation |
| --- | --- |
| `event` | Coarse speech, silence (`sil`), and noise intervals |
| `text` | Time-aligned corrected transcript spans |
| `sentence` | Finer-grained speech, silence, and noise intervals within sentences |

The detailed JSON records additionally provide `timestamp.silence`, `timestamp.noise`, and `timestamp.sentence` arrays. Interval boundaries are measured in **seconds**; nested `items` represent finer-grained events inside sentence segments.

## Dataset Availability and Use

This repository distributes **complete JSON annotations and split metadata**, but only **nine paired examples of audio and TextGrid annotations**. Access to the repository or to its JSON files should not be interpreted as access to the complete WAV/TextGrid collection.

PSC-Speak-1500 is intended for research into Mandarin speaking assessment, expert-scoring agreement, and speech/text annotation. Any additional access permissions and data-use conditions should be confirmed with the dataset maintainers.
