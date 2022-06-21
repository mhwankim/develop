import pandas as pd
import os

os_path = "C:/Users/6904115/Desktop/python"
os.chdir(os_path)
srt_de = "2020-01-02"

data_1 = pd.read_csv(os_path + "/139130.KS.csv")[["Date", "Close"]]
data_2 = pd.read_csv(os_path + "/005930.KS.csv")[["Date", "Close"]]

def pretreat_df(DataFrame, srt_de):
    for i in DataFrame.index:
        if DataFrame.iloc[i]["Date"] == srt_de:
            break
    df = DataFrame[i:].copy()
    df.reset_index(drop = True, inplace = True)
    
    data_lst = []
    
    for j in df.index:
        if j == 0:
            data_lst.append(int(0))
        else:
            if df.iloc[j-1]["Close"] > df.iloc[j]["Close"]:
                data_lst.append(int(-1))
            elif df.iloc[j-1]["Close"] > df.iloc[j]["Close"]:
                data_lst.append(int(1))
            else:
                data_lst.append(int(0))
    df["spl"] = data_lst
    df.set_index("Date", drop = True, inplace = True)
    return df

def correlation_score(DataFrame_1, DataFrame_2):
    data_num = DataFrame_1.count()[1]
    mean_1 = DataFrame_1.mean(axis = 0)[1]
    mean_2 = DataFrame_2.mean(axis = 0)[1]
    sum = 0
    for i, date in enumerate(DataFrame_1.index):
        x_1 = DataFrame_1.iloc[i]["spl"] - mean_1
        x_2 = DataFrame_2.iloc[i]["spl"] - mean_2
        sum += x_1*x_2
    
    score = sum/(DataFrame_1.std()[1]*DataFrame_2.std()[1])
    return score/data_num

data_3 = pretreat_df(data_1, srt_de)
data_4 = pretreat_df(data_2, srt_de)

print(correlation_score(data_3, data_4))
