# Tensorflow-char-rnn

## 介绍

用tensorflow 框架的 rnn实现。 采用了[tensorflow_poems](https://github.com/jinfagang/tensorflow_poems) 的 model，参考 [char-rnn](https://github.com/karpathy/char-rnn) 对数据处理作了改进，除了中文(唐诗)外也可以处理任意语料。

[tensorflow_poems](https://github.com/jinfagang/tensorflow_poems)中把唐诗按行切分，人工清洗全唐诗中的数据后切分为汉字，并在每行数据首尾填充sos(B)和eos(E)以辅助训练，这些举措其实引入了一些先验知识，所以效果比较好，能较快的学会唐诗格律。作出类似以下的诗

```
雨霁开门中，山听淮水流。
落花遍霜霰，金壶横河湟。
年年忽息世，径远谁论吟。
惊舟望秋月，应柳待晨围。
人处山霜月，萧萧广野虚。
```

但这个方法不够泛化，有没有不需要先验知识就能处理的方法呢？[char-rnn](https://github.com/karpathy/char-rnn) 给出了另一种思路，把文本看作 **二进制流**， 这样完全不需要数据清洗，甚至不用关心编码问题。把二进制流按照固定长度(train_sequence_len)截断成定长的训练数据灌給模型，训练出模型后再来看效果。这样作的好处有两个，一是word少(二进制的byte取值空间最多有255)模型参数也就少，另一个是鲁棒性更高，任何文件拿来都能学。像编码/换行，理论上说都是一种模式，只要模型学习能力够强自然能学到。

```
秋雨:桐花连，露淋烟梨叶螺。蓝燕晚花红态结，谢娘钗下泪不明，坐来秋。佩寒敲，袅袅金钗挂唤筝，碧玉楼前夹银耳。
也怜银线烘不迷，楼垂翡翠。镂香斋，弱碎一片轻香残月高。君无问尔酝，嫁与人间春。采果二，送，直家风，夜闺心，东岸上，想庭前，还不在，日落不为。莫共飘芳似旭，忍复把花同。微翠红，皎皎红丝盈红。
```

这是全唐诗上跑了一百个迭代后的效果，没有之前好，勉强能看。

```
第一千四百一十二章大笑的青城，再次淡淡的望着防御，然后，一道龙塔应喝，石像，微微归忙转向萧炎，这般感觉，大家还能看不觉。

    这大声了，随成一初死，不知，死合，果然有斗破施展，竟然会帝月。”百起身来血色那巨鹰中心火翼暴涨，随着这道彩光柱?四溅，手臂到了极为虚影之中不断的后中的诡异

    第一千四百二十七章帝着各杂的身影，自然不是一个番吧

    而这些尽数桀骸的荣一创凝聚，费天苦笑道。

    黑灿的加入，萧炎眼中入有人肩庇林，数人不死的落入了提升，在那异样古龙岛上，太古元与老师不见，那种并不乏字，他父般的中州，大多都贇的必然不可能够插手，不少斗帝强者的应该是我，也其听说过，这是整个中州，这里，你继续当年的动静！

    “凭两，现在的确极大，以魂元天都是会是在那样的手段，你知道八  “你的要恼不到形，天府联盟与务劲也明显梦惹起来。”

    此刻的萧炎，这些团落之上，可并未吸响贪，再去过先岔爆！

```

这是《斗破苍穹》跑了20个迭代后的效果。

## 如何跑

参考自带的sh脚本，以dpcq开头的是《斗破苍穹》，以jinyong开头的是《金庸全集》，以poem开头的是《全唐诗》，以shakespeare开头的是《莎士比亚》。

安装了python3+ tf之后就可以跑了，最好用GPU，速度会快一些。

脚本中用了busybox，主要是为了解决虚拟机环境中tail -f 在console不刷新的问题，如果不是在虚拟机环境中跑，去掉即可

