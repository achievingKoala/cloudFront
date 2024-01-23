<template>
    <div>
        <p>
            <h3>hello</h3>
            Chinese Sentence:{{ testObj.id }}. {{ testObj.chinese }}
        </p>
        <div>
            <!-- <label for="english-sentence">English Sentence:</label> -->
            <textarea id="english-sentence" @keyup.enter="nextSentence" v-model="userInput"></textarea>
        </div>
        <button @click="lastSentence">last</button>
        <button @click="playSound">play sound</button>
        <button @click="ifShowCorrect = !ifShowCorrect">show correct</button>
        <button @click="nextSentence">next</button>
        <br>
        <div>
            if correct :{{ userInput == testObj.sentence }}
            <p v-if="ifShowCorrect">
                correct : <span v-for="(word, index) in words" :key="index"
                    :style="{ color: getColorClass(word, index) }">{{ word
                        + " " }}</span>
            </p>
            <p>
                <span v-for="(word, index) in userInput.split(' ')" :key="index"
                    :style="{ color: getColorClass(words[index], index) }">{{ word + " " }}</span>
            </p>
        </div>
    </div>
</template>
  
<script>
import axios from 'axios';

export default {
    data() {
        return {
            ifShowCorrect: false,
            currIndex: 0,
            testObj: {
                id: 1,
                sentence: "Many people believe that we have developed into a throw-away society.",
                chinese: "许多人认为我们已经发展成为一个一次性社会",
            },
            userInput: "",
            testList: [],
            appkey: 'hwwIxo4LqUXVx46S',
            token: '763ff0f0d0e84959a9567c5bd05dfdb6',
            format: 'wav',
            sampleRate: 16000
        };
    },
    mounted() {
        window.addEventListener("keydown", this.handleKeyPress);
        var currDate = new Date().getDate();
        this.token = localStorage.getItem('token');
        if (currDate != localStorage.getItem('date')) {
            localStorage.setItem('date', currDate);
            this.getToken()
        }
        if (this.token == null) {
            this.getToken()
        }
        const list3 = [
            {
                "id": 10,
                "sentence": "It is widely acknowledged that climate change is a pressing global issue that requires immediate attention.",
                "chinese": "众所周知，气候变化是一个迫切需要立即关注的全球性问题。"
            },
            {
                "id": 11,
                "sentence": "This essay aims to explore the various factors that contribute to the rise of populism in politics.",
                "chinese": "本文旨在探讨导致民粹主义在政治中兴起的各种因素。"
            },
            {
                "id": 12,
                "sentence": "One key aspect to consider is the impact of social media on interpersonal relationships.",
                "chinese": "要考虑的一个关键方面是社交媒体对人际关系的影响。"
            },
            {
                "id": 13,
                "sentence": "It is imperative to address the issue of income inequality to ensure a fair and just society.",
                "chinese": "解决收入不平等问题是确保社会公平正义的当务之急。"
            },
            {
                "id": 14,
                "sentence": "This phenomenon can be attributed to a range of factors, including economic globalization and cultural homogenization.",
                "chinese": "这种现象可以归因于一系列因素，包括经济全球化和文化同质化。"
            },
            {
                "id": 15,
                "sentence": "It is evident that there is a strong correlation between education and socioeconomic status.",
                "chinese": "很明显，教育和社会经济地位之间有很强的相关性。"
            },
            {
                "id": 16,
                "sentence": "Furthermore, it should be noted that the rise of automation poses a threat to job security.",
                "chinese": "此外，应该指出的是，自动化的兴起对工作保障构成了威胁。"
            },
            {
                "id": 17,
                "sentence": "It is crucial to highlight the potential consequences of unchecked deforestation on biodiversity loss.",
                "chinese": "强调不加控制的森林砍伐对生物多样性丧失的潜在后果至关重要。"
            },
            {
                "id": 18,
                "sentence": "It is essential to strike a balance between economic growth and environmental sustainability.",
                "chinese": "在经济增长和环境可持续性之间取得平衡至关重要。"
            },
            {
                "id": 19,
                "sentence": "It is imperative to adopt measures to mitigate the adverse effects of air pollution on public health.",
                "chinese": "必须采取措施减轻空气污染对公众健康的不利影响。"
            },
            {
                "id": 20,
                "sentence": "This trend has both positive and negative implications for the future of work.",
                "chinese": "这一趋势对未来的工作既有积极的影响，也有消极的影响。"
            },
            {
                "id": 21,
                "sentence": "It is undeniable that this issue poses a significant challenge for policymakers and governments worldwide.",
                "chinese": "不可否认，这一问题对世界各国的政策制定者和政府构成了重大挑战。"
            },
            {
                "id": 22,
                "sentence": "In conclusion, it is evident that the benefits of renewable energy outweigh the drawbacks of fossil fuel dependency.",
                "chinese": "总之，很明显，可再生能源的好处超过了依赖化石燃料的缺点。"
            }
        ]
        const list2 = [{
            "id": 10,
            "sentence": "Children intend to imitate what they have seen on mass media , which is sometimes dangerous and harmful",
            "chinese": "孩子们倾向于模仿他们在大众传媒上看到的东西，这有时是危险和有害的。"
        },
        {
            "id": 11,
            "sentence": "In spite of a lot of conveniences that cars bring to people's life , it can create a series of serious problems",
            "chinese": "尽管汽车给人们的生活带来了许多便利，但是它也产生了一系列严重的问题。"
        },
        {
            "id": 12,
            "sentence": "It has been a few decades since the computer came into being",
            "chinese": "自从计算机出现以来已经有几十年了。"
        },
        {
            "id": 13,
            "sentence": "Once you change your present job , you will be faced with the danger of being unemployed",
            "chinese": "一旦你换了现在的工作，你将面临失业的危险。"
        },
        {
            "id": 14,
            "sentence": "Not only does studying in school serve academic purpose , but students learn how to handle interpersonal relations",
            "chinese": "在学校学习不仅是为了学术目的，而且还能学会如何处理人际关系。"
        },
        {
            "id": 15,
            "sentence": "Providing more park areas , in the long run , has proved to be a practical way in many buildings in the world",
            "chinese": "从长远来看，提供更多的公园区域已被证明是世界上许多建筑物的实用方法。"
        },
        {
            "id": 16,
            "sentence": "Owing to fierce competition in today's world , a great many young people find themselves under great pressure",
            "chinese": "由于当今世界竞争激烈，许多年轻人感到压力很大。"
        },
        {
            "id": 17,
            "sentence": "I absolutely cannot stress enough the importance of education in shaping a better future for individuals and society as a whole",
            "chinese": "教育在为个人和整个社会塑造更美好未来方面的重要性再怎么强调也不为过。"
        },
        {
            "id": 18,
            "sentence": "It is absolutely imperative that we take immediate action to address the pressing issue of climate change before irreparable damage is done to our planet",
            "chinese": "在对我们的星球造成无法弥补的损害之前，我们绝对有必要立即采取行动解决气候变化这一紧迫问题。"
        },
        {
            "id": 19,
            "sentence": "Parents would not expect their children to become useful individuals without working hard",
            "chinese": "父母不会期望他们的孩子不努力就成为有用的人。"
        }]
        const list1 = [
            {
                "id": 1,
                "sentence": "Many people believe that we have developed into a throw-away society",
                "chinese": "许多人认为我们已经发展成为一个一次性社会"
            },
            {
                "id": 2,
                "sentence": "If a person loses a job but can get assistance , we can say this country is advanced",
                "chinese": "如果一个人失去了工作，但能得到帮助，我们可以说这个国家是先进的"
            },
            {
                "id": 3,
                "sentence": "While traditional buildings might look nice from the outside , they are often not very user-friendly",
                "chinese": "虽然传统建筑从外面看起来很漂亮，但它们通常不太便于使用"
            },
            {
                "id": 4,
                "sentence": "Students who simply rely on teachers for knowledge are not really learning",
                "chinese": "单纯依靠老师获取知识的学生并没有真正学到知识"
            },
            {
                "id": 5,
                "sentence": "If fuel prices go up , either fewer people will drive or people will drive less , which makes sense for reducing pollution",
                "chinese": "如果燃料价格上涨，要么开车的人减少，要么开车的人减少，这对减少污染是有意义的"
            },
            {
                "id": 6,
                "sentence": "Whether mobile phones bring more harm than good to us has caused heated debate",
                "chinese": "手机是否对我们弊大于利已经引起了激烈的争论"
            },
            {
                "id": 7,
                "sentence": "It is obvious that human activities have the greatest impact on the environment",
                "chinese": "很明显，人类活动对环境的影响最大"
            },
            {
                "id": 8,
                "sentence": "Nowadays , an increasing number of people are concerned about the phenomenon that our kids are spending more time watching TV",
                "chinese": "现在，越来越多的人关心我们的孩子花更多的时间看电视这一现象"
            },
            {
                "id": 9,
                "sentence": "This is because it is the rich and powerful people in our society who are able to impose changes",
                "chinese": "这是因为在我们的社会中，有钱有势的人能够强加变革"
            }
        ]
        const list4 = [
            {
                "id": 2,
                "sentence": "This writing will discuss reasons why landowners decide to have an interest in the buildings they live in and how they manage to have the information.",
                "chinese": "本文将讨论土地所有者决定对他们居住的建筑物感兴趣的原因，以及他们如何设法获得这些信息。"
            },
            {
                "id": 3,
                "sentence": "One of the primary reasons for this trend is the desire to connect with the past.",
                "chinese": "这种趋势的主要原因之一是人们渴望与过去联系。"
            },
            {
                "id": 4,
                "sentence": "By learning about the history of their homes, people can gain a better understanding of the people who lived there before them, their way of life, and their contributions to society.",
                "chinese": "通过了解他们家园的历史，人们可以更好地了解在他们之前住在那里的人，他们的生活方式，以及他们对社会的贡献。"
            },
            {
                "id": 5,
                "sentence": "Discovering the history of their house or building satisfies their curiosity and adds depth to their personal connection with the space.",
                "chinese": "探索他们的房子或建筑的历史满足了他们的好奇心，并增加了他们与空间的个人联系的深度。"
            },
            {
                "id": 6,
                "sentence": "Speaking with long-term residents or neighbors can provide valuable stories about the house or building.",
                "chinese": "与长期居民或邻居交谈可以提供有关房屋或建筑物的宝贵故事。"
            },
            {
                "id": 7,
                "sentence": "These personal accounts can offer a unique perspective and fill in gaps that may not be found in official records.",
                "chinese": "这些个人描述可以提供一个独特的视角，填补官方记录中可能找不到的空白。"
            },
            {
                "id": 8,
                "sentence": "By understanding its architectural style, original features, and historical context, individuals can make informed decisions on renovation projects.",
                "chinese": "通过了解其建筑风格、原始特征和历史背景，个人可以在改造项目上做出明智的决定。"
            },
            {
                "id": 9,
                "sentence": "It's important to note that the availability and accessibility of historical records may vary depending on the country and local resources.",
                "chinese": "重要的是要注意，历史记录的可用性和可访问性可能因国家和当地资源而异。"
            },
            {
                "id": 10,
                "sentence": "Therefore, individuals should explore a combination of these research methods to gather a comprehensive understanding of the history of their house or building.",
                "chinese": "因此，个人应该探索这些研究方法的组合，以全面了解他们的房子或建筑的历史。"
            }
        ]
        this.testList = list4
        // this.testList = list2
        this.testObj = this.testList[0]
    },
    computed: {
        words() {
            return this.testObj.sentence.split(" ");
        },
    },
    methods: {
        getToken() {
            axios.get('https://service-h95mmpda-1301472420.gz.apigw.tencentcs.com/api/token', {
            })
                .then(response => {
                    // Handle the successful response
                    console.log(response.data);
                    // Do something with the data
                    this.token = response.data.token;
                    localStorage.setItem('token', this.token);
                })
                .catch(error => {
                    // Handle the error
                    console.error(error);
                    // Show error message to the user
                });
        },
        handleKeyPress(event) {
            // event.key === 'Control' && 
            if (event.key === 'p' && event.ctrlKey) {
                console.log('ambiguous===>')
                this.playSound()
            }
            if (event.key === 's' && event.ctrlKey) {
                this.ifShowCorrect = !this.ifShowCorrect
            }
            if (event.key === 'n' && event.ctrlKey) {
                this.nextSentence()
            }
        },
        playSound() {
            var word = this.testObj.sentence
            var textUrlEncode = encodeURIComponent(this.testObj.sentence)
                .replace(/[!'()*]/g, function (c) {
                    return '%' + c.charCodeAt(0).toString(16);
                });
            var url = 'https://nls-gateway-cn-shanghai.aliyuncs.com/stream/v1/tts';
            url = url + '?appkey=' + this.appkey;
            url = url + '&token=' + this.token;
            url = url + '&text=' + textUrlEncode;
            url = url + '&format=' + this.format;
            url = url + '&sample_rate=' + this.sampleRate;
            console.log("playing:======", word, word.length)
            new Audio(url).play()
        },
        nextSentence() {
            this.currIndex++;
            if (this.currIndex == this.testList.length) {
                this.currIndex = 0;
            }
            this.testObj = this.testList[this.currIndex]
            // this.playSound()
            this.userInput = ""
        },
        lastSentence() {
            this.currIndex--;
            if (this.currIndex == -1) {
                this.currIndex = this.testList.length - 1;
            }
            this.testObj = this.testList[this.currIndex]
            this.userInput = ""
        },
        getColorClass(word, index) {
            var userWord = this.userInput.split(" ")[index]
            if (this.userInput.split(" ")[index] === word || this.words.includes(userWord)) {
                return "green";
            } else {
                return "red";
            }
        },
    },
};
</script>
  
<style>
.green {
    color: green;
}

.red {
    color: red;
}

#english-sentence {
    width: 1000px;
    height: 176px;
    font-size: 40px;
}
</style>
  