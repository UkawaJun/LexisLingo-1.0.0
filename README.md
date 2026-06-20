# LexisLingo-1.0.0


# 創発システム知能専攻-Research 01: Contextual Lexicon Project



## LexisLingo.py

```mermaid
graph TD
    subgraph ENTRY["Entry & Navigation"]
        MW["MainWindow\n─────────────\nstack: QStackedWidget\ngo_to_learning()\ngo_to_course()"]
        LD["LoginDialog\n─────────────\ninput_user\ninput_pwd\ncheck_login()"]
    end

    subgraph COURSE["Course Selection"]
        CSV["CourseSelectionView\n─────────────\nmodule_list\nscroll_area\nflow_layout\nload_chapters_by_module()\non_chapter_clicked()\n_reposition_avatar()\nopen_review_entry()"]
        CC["ChapterCard\n─────────────\ntitle / progress_val\nupdate_background()\n_parse_progress()"]
        FL["FlowLayout\n─────────────\ndoLayout()\nheightForWidth()"]
    end

    subgraph LEARN["Learning View"]
        LV["LearningView\n─────────────\ntext_browser\nmedia_player\nmedia_stack\nmedia_splitter\nload_article()\non_word_clicked()\n_on_finish_clicked()\ncheck_local_progress()\nstart_batch_download()\nreset_progress_view()\n_toggle_note_panel()\n_load_note() / _save_note()\n_export_pdf()"]
        EPD["ExamPickerDialog\n─────────────\ntotal / learned\ncandidate_count\n_spin / _on_ok()"]
        SD["SentenceDialog\n─────────────\nvoice_combo\nspeed_combo\non_download()\non_play()\n_start_download()\n_play_async()"]
    end

    subgraph REVIEW["Review System"]
        RED["ReviewEntryDialog\n─────────────\nslider / spin\nlbl_eligible\nlbl_mastery\nlist_picked\n_on_search()\n_on_generate()\n_on_start()\n_refresh_picked()"]
        RD["ReviewDialog\n─────────────\n_queue / _q_idx\n_word_wrong\n_build_queue()\n_load_item()\n_judge()\n_judge_trans()\n_judge_char_select()\n_build_char_buttons()\n_build_choice_buttons()\n_on_choice()\n_insert_char()\n_finish()\n_show_result()\n_on_close_result()\n_on_exit()"]
    end

    subgraph AVATAR["User Panel"]
        AB["AvatarBall\n─────────────\nrefresh()\n_show_panel()\n_toggle_panel()"]
        UIP["UserInfoPanel\n─────────────\n_build_ui()\nrefresh()"]
    end

    subgraph WORKERS["Background Workers"]
        SPW["_SentencePlayWorker\n─────────────\natlas / sentence\nrun()"]
        BDW["BatchDownloadWorker\n─────────────\nword_tasks\nglobal_pool\nrun()"]
        CPB["CircularProgressButton\n─────────────\nset_progress()\npaintEvent()"]
        DCB["DownloadCircleButton\n─────────────\nprogress\npaintEvent()"]
    end

    MW --> CSV
    MW --> LV
    MW --> LD
    CSV --> CC
    CSV --> FL
    CSV --> AB
    CSV --> RED
    RED --> RD
    LV --> EPD
    LV --> SD
    LV --> CPB
    LV --> DCB
    LV --> BDW
    AB --> UIP
    SD --> SPW
```

---

## Config.py

```mermaid
graph TD
    subgraph UI_DIALOG["UI — Dialogs"]
        WCD["WordCardDialog\n─────────────\nword / mastery_score\nlast_date\nload_word_data()\nplay_audio()\nconfirm_mastered()\nopen_quiz()\n_FindOrgin()\nupdate_pbar_style()\n_start_silent_upgrade()"]
        QMP["QuizModePicker\n─────────────\n_pick(mode)"]
        QD["QuizDialog\n─────────────\nwords / mode\n_build_mixed_queue()\n_load_item()\n_judge()\n_judge_trans()\n_finish()\n_show_result()\n_insert_char()"]
        ED["ExamDialog\n─────────────\nselected\n_build_queue()\n_load_item()\n_judge()\n_judge_trans()\n_on_exit()\n_finish()\n_show_result()\n_on_close_result()\n_start_theme_anim()"]
    end

    subgraph AUDIO["Audio System"]
        SAA["SmartAudioAtlas\n─────────────\nmaster_index\nplay_speed()\nget_or_build()\n_load_index()\n_save_index()\n_enforce_max()\n_evict()\n_safe_filename()\ntrim_silence()"]
        ABW["AudioBuilderWorker\n─────────────\nword / voice\nrun()"]
        QPW["_QuizPlayWorker\n─────────────\nword / speed_idx\nrun()"]
        LPW["_LoopPlayWorker\n─────────────\nword / gap_ms\nrun() / stop()"]
    end

    subgraph REVIEW_SYS["Review System"]
        RQG["ReviewQuestionGenerator\n─────────────\npicked_words / all_info\ngenerate()\n_gen_word()\n_q_listen()\n_q_cn_to_en()\n_q_sentence_fill()\n_q_sentence_choice()\n_print_results()"]
        RSM["ReviewSessionManager\n─────────────\n_items / _store\n_udict\nsubmit_answer()\nnext_question()\n_judge()\n_settle_word()"]
        RAC["rv_apply_correct()\n─────────────\ninfo / target_tra\ncorrect_q / wrong_q"]
        RAW["rv_apply_wrong()\n─────────────\ninfo / target_tra\ncorrect_q / wrong_q"]
        RH["Helpers\n─────────────\n_rv_build_soft_regex()\n_rv_decode_tra()\n_rv_split_meaning()\n_rv_hanzi()\n_rv_ascii_dist()"]
        RMI["RV_MIN_INTERVAL_SEC"]
    end

    subgraph ALGO["Algorithm Layer"]
        RP["review_priority()\n─────────────\ninfo / now_sec\n→ 0~1 or None"]
        CR["compute_relevel()\n─────────────\ninfo\n→ stage int"]
        AN["_as_num()"]
        IU["is_unlearned()"]
        IM["is_mastered()"]
        STAGE["STAGE_INTERVAL_SEC\n─────────────\n9-stage intervals"]
        WOP["_WOP\n─────────────\nfield key mapping"]
    end

    subgraph DB_LAYER["Database Layer"]
        ISQ["initUWSSQL()"]
        BWS["BuildWordDicSqlite()"]
        BFS["BackFromWordSqlite()"]
        DBI["db_init()"]
        DBW["db_Write()"]
        DBR["db_Read()"]
        DBRA["db_ReadAll()"]
        CUD["ChangeUserDailyDate()"]
        GUD["GetUserDailyDate()"]
        UDD["UserDailyDay()"]
    end

    subgraph UTILS["Utils"]
        SS["_save_screenshot()"]
        ADE["_addExpose()"]
        IE["increaseExpose()"]
    end

    WCD --> SAA
    QD --> SAA
    ED --> SAA
    SAA --> ABW
    SAA --> QPW
    WCD --> LPW
    RQG --> RH
    RQG --> ALGO
    RSM --> RAC
    RSM --> RAW
    RAC --> ALGO
    RAW --> ALGO
    ED --> CUD
    RSM --> CUD
    WCD --> BFS
```

---

## _Enviro.py

```mermaid
graph TD
    subgraph STORE["Storage"]
        UWSS["UserWordStoreSQLite\n─────────────\ndb_path / crypt_key\nget() / set()\nhas() / keys()\nread_all() / write_all()\nget_many()\nget_or_create()\nget_examed_set()\ncount() / close()\n_encode() / _decode()\n_init_schema()"]
    end

    subgraph CRYPTO["Encryption"]
        AED["ASCLL_EnDecryption()"]
        EC["EndeCrypt()"]
        FER["File_EnDeRead()"]
        FEW["File_EnDeWrite()"]
    end

    subgraph AUDIO_E["Audio SFX"]
        PNW["play_new_word_sfx()"]
        PWS["play_wrong_sfx()"]
    end

    subgraph UTILS_E["Utils"]
        DP["DebugPrinter\n─────────────\nDEBPrint()\nDEBAnimaPrint()\n_writeDiary()\nreset()"]
        RCL["readCsvToList()"]
        GRK["get_random_keys()"]
        GWC["get_word_color()"]
        PR["phoneticRepair()"]
        CTC["copy_to_clipboard()"]
        IC["initConfig()"]
        GFC["genFrequentCharts()"]
    end

    FER --> EC
    FEW --> EC
    EC --> AED
    UWSS --> EC
```

---

## LexisLingo ↔ Config Interaction

```mermaid
sequenceDiagram
    participant MW as MainWindow
    participant CSV as CourseSelectionView
    participant LV as LearningView
    participant RED as ReviewEntryDialog
    participant RD as ReviewDialog
    participant WCD as WordCardDialog
    participant ED as ExamDialog
    participant SAA as SmartAudioAtlas
    participant RSM as ReviewSessionManager
    participant RQG as ReviewQuestionGenerator
    participant UWSS as UserWordStoreSQLite
    participant DB as DailyDate Functions

    MW->>CSV: show course list
    CSV->>UWSS: read_all() → calc progress
    CSV->>RED: open_review_entry()

    RED->>UWSS: read_all()
    RED->>RED: review_priority() filter eligible words
    RED->>RQG: generate(picked_words, all_info)
    RQG-->>RED: generated_questions[]
    RED->>RD: start exam

    RD->>RSM: ReviewSessionManager(questions, store, udict)
    loop each question
        RD->>RD: _load_item() → display
        RD->>SAA: play_speed(word, idx)
        RD->>RD: _judge(ok)
        RD->>RD: _word_wrong[word] += 1
    end
    RD->>RSM: (not used for settle)
    RD->>RD: _finish()
    RD->>UWSS: get(word) → rv_apply_correct/wrong → set(word)
    RD->>DB: ChangeUserDailyDate(re/er/n)

    LV->>WCD: on_word_clicked()
    WCD->>UWSS: get_or_create(word)
    WCD->>SAA: play_speed()
    WCD->>UWSS: set(word, updated_info)
    WCD->>DB: ChangeUserDailyDate(x)

    LV->>ED: _on_finish_clicked()
    ED->>UWSS: get(word) → update → set(word)
    ED->>DB: ChangeUserDailyDate(ex/ee/n)
```

---

## Module Dependencies

```mermaid
graph LR
    LL["LexisLingo.py"]
    CF["Config.py"]
    EV["_Enviro.py"]

    LL -->|"ReviewDialog\nReviewEntryDialog\nLearningView\nCourseSelectionView"| CF
    LL -->|"SmartAudioAtlas\nExamDialog\nQuizDialog\nWordCardDialog\nreview_priority\nrv_apply_*\nChangeUserDailyDate"| CF
    CF -->|"UserWordStoreSQLite\nFile_EnDeRead/Write\nplay_*_sfx\nDebugPrinter"| EV
    LL -->|"QThread workers\nSFX functions"| EV
```






本报告旨在展示初步量化分析结果，重点聚焦于词汇原型（Lemmas）的提取与形态学基础特征分布。
This report presents the preliminary quantitative analysis, focusing on the extraction of word lemmas and the distribution of fundamental morphological characteristics.

---

### 1. 高频核心词汇原型提取 (Top 30 Most Frequent Lemmas)

以下图表直观呈现了语料库中经过形态学回溯后，出现频次最高的前 30 个单词原型。作为动态语境生成的基础，这些核心词汇构成了初始语义网络的锚点。

The following chart illustrates the top 30 most frequently occurring word lemmas in the corpus after morphological tracing. Serving as the foundation for dynamic context generation, these core vocabulary items act as the anchor points of the initial semantic network.

<p align="center">
  <img src="https://github.com/user-attachments/assets/1bd03f46-6b50-4830-954c-d35c208c76fc" alt="Top 30 Most Frequent Lemmas" width="85%">
  <br>
  <em>Fig 1: 频次最高的前30个单词原型分布 / Top 30 Word Lemmas Distribution</em>
</p>

---

### 2. 词汇复杂度与长度分布 (Word Length Frequency Distribution)

本节展示了不同长度单词的频次分布模型。该分布特征客观反映了语料库的词汇复杂度。

This section displays the frequency distribution across different word lengths. This characteristic objectively reflects the lexical complexity of the corpus.


<p align="center">
  <img src="https://github.com/user-attachments/assets/23b57728-5371-470e-83f1-d84efbe4aefb" alt="Word Length Frequency Distribution" width="85%">
  <br>
  <em>Fig 2: 单词长度与出现频率的映射关系 / Mapping of Word Length to Frequency</em>
</p>



---
> **Note:** 演示数据集基于闭环语境过滤生成，完整链式释义网络记录详见后续更新。 
> The demo dataset is generated based on closed-loop context filtering. The complete chained semantic network records will be detailed in subsequent updates.
