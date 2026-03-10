![[Pasted image 20260310115732.png]]
config.json and pytorch_model.bin这个是预训练结束后得到的模型权重（用microsoft deberta base进行的迁移学习，该模型可以在kaggle上直接add input dataset导入）注意后续基于这个进行训练后面的教师模型需要保证tokenizer是和这个一致的。
```
AutoConfig.from_pretrained(CFG.model)  # 可以通过这样的方式导入预训练结束的模型
AutoModel.from_pretrained(CFG.model)
```
mlm.py 是预训练的执行脚本（一般后面训练教师模型，只要用上面的两个就行了）
另外两个events.out.tfevents是TensorBoard 日志文件，写报告的哥们如果有需要的话，通过这个可以看 loss 曲线和看训练过程记录
# baseline output
![[Pasted image 20260310121636.png]]

-  **基础模型与 tokenizer**
    
    - `deberta-base/`
        
    - `tokenizer/`
        
- **训练 checkpoint / 最优模型**（主要我用来恢复训练和evaluation用的）
    
    - `-kaggle-working-deberta-base_fold*_best.pth`
        
    - `-kaggle-working-deberta-base_fold*_latest_checkpoint.pth`
        
- **训练结果汇总**
    
    - `oof_df.pkl`
        
- **训练记录 / 辅助信息**
    
    - `fold*_best_score_info.txt` 这里包含的是对应fold的最好的cv score
        
    - `train.log`详细的训练过程日志

- 重要提醒：后面的模型训练都是5个模型加上5fold训练时间很长，一定要做好resume train的设置，另外kaggle每次session都会清空output，所以重要文件一定要本地备份
- 另外baseline中使用的deberta base是从hugging face上下载的，但是我预训练的deberta base是通过kaggl自己的库中通过add dataset加入的，这两个版本不能保证模型参数和tokenizer是一样的，所以后面训练的教师模型等都要align我用的预训练的模型