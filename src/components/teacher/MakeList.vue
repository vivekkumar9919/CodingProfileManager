<template>
    <div>
    <div class="topimgContainer">
      <div class="topimageBox">
        <div id="topheading">
          <div id="heading1">Make Student List</div>
          <div id="headingContent">
          This project is develop to integrate the profile data of leetcode, codechef and codeforces at one place with other more features.
          </div>
        </div>
      </div>
    </div>

 
    <div class="freindinputCont">
      <form class="row g-3 freindinputbox" @submit.prevent="addfriendfunc" id="formid">
        <div id="selectinput">
          <select
            class="form-select"
            aria-label="Default select example"
            required
            v-model="platformselect"
          >
            <option value="">Select Platform</option>
            <option value="codechef">Codechef</option>
            <option value="codeforces">Codeforces</option>
            <option value="leetcode">Leetcode</option>
          </select>
        </div>

        <div class="col-auto">
          <label for="inputuser" class="visually-hidden">Username</label>
          <input
            type="text"
            class="form-control"
            id="inputuser"
            placeholder="@username"
            required
            v-model="inputusername"
          />
        </div>
        <div class="col-auto">
          <button type="submit" class="btn btn-primary mb-3">
            Add Friends
          </button>
        </div>
      </form>
    </div>

    <!-- friend output container -->

    <!-- codechef block -->
    <div class="outputCont">
      <div class="codecheflist platform" id="codechefid">
        <div class="card-header card heading">Codechef</div>
        <table class="table table-striped table-hover">
          <thead>
            <tr>
      
              <th scope="col">Username</th>
              <th scope="col">Stars</th>
              <th scope="col">Rating</th>
              <th scope="col">Highest Rating</th>
              <th scope="col">Global Rank</th>
              <th scope="col">Country Rank</th>
            </tr>
          </thead>
          <tbody id="addrowcodechef"></tbody>
        </table>
      </div>
      <!-- codeforces block -->
      <div class="codeforceslist platform" id="codeforces">
        <div class="card-header card heading">Codeforces</div>
        <table class="table table-striped table-hover">
          <thead>
            <tr>
              <th scope="col">Name</th>
              <th scope="col">Username</th>
              <th scope="col">Rating</th>
              <th scope="col">Maximum Rating</th>
              <th scope="col">Rank</th>
              <th scope="col">Maxium Rank</th>
              <th scope="col">Contribution</th>
            </tr>
          </thead>
          <tbody id="addrowcodeforces"></tbody>
        </table>
      </div>

      <!-- leetcode block -->
      <div class="leetcodelist platform" id="leetcode">
        <div class="card-header card heading">Leetcode</div>
        <table class="table table-striped table-hover">
          <thead>
            <tr>
              <th scope="col">Username</th>
              <th scope="col">Total Problems Solved</th>
              <th scope="col">Easy Problems</th>
              <th scope="col">Medium Problems</th>
              <th scope="col">Hard Problems</th>
            </tr>
          </thead>
          <tbody id="addrowleetcode"></tbody>
        </table>
      </div>
    </div>


    </div>
</template>

<script>
import axios from "axios";
export default {
  name: "MakeList",
  data() {
    return {
      friendlist: {},
      errmsg: "",
      userdata: {},
      inputusername: "",
      platformselect: "",
                contestRanking:[],
             toatal_solved:'',
             easy_solved:'',
             medium_solved:'',
             hard_solved:'',
    };
  },

  methods: {
    async addfriendfunc() {
    
      const DataByUser = await JSON.stringify({
        email: localStorage.getItem("email2"),
        username: this.inputusername,
        platform: this.platformselect,
      });
      // console.log(DataByUser);
      let customConfig = {
        headers: {
          "Content-Type": "application/json",
        },
      };

      await axios
        .post(
          `api/teacher/list/add_student`,
          DataByUser,
          customConfig
        )
        .then(() => {

       
          alert("Student added successfully");
          document.getElementById('formid').reset();
          // location.reload();
        })
        .catch((err) => {
          console.log(`nhi hua bhai ye rha errr ${err}`);
        });
        location.reload();
    },
  },
  async mounted() {
    //  feching data from server
    await axios
      .get(
        "api/teacher/list/showing_student/" +
          localStorage.getItem("email2")
      )
      .then((response) => {
        // console.log(response.data);
        this.friendlist = response.data;
        // console.log(this.friendlist[0]["platform"]);
        // console.log(this.friendlist[0]["username"]);
      })
      .catch((error) => {
        console.log(error);
        this.errmsg = "Error in fetching data";
      });

    //   showing data to the web page

    for (var i = 0; i < this.friendlist.length; i++) {

      if (this.friendlist[i]["platform"] == "codechef") {
      
        await axios.get("api/codechef/user/"+this.friendlist[i]["username"])
          .then((res) => {
            // console.log(res.data);
            this.userdata = res.data;
            // console.log(this.userdata)
          })
          .catch((error) => {
            console.log(error);
            this.errmsg = "Error in fetching data";
          });
        const boxWrapper = document.getElementById("addrowcodechef");

        const box = document.createElement("tr");
        
        box.innerHTML = `
              
              <td>${this.friendlist[i]["username"]}</td>
              <td>${this.userdata.rating}</td>
              <td>${this.userdata.rating_number}</td>
              <td>${this.userdata.max_rank}</td>
              <td>${this.userdata.global_rank}</td>
              <td>${this.userdata.country_rank}</td>
      `;
        boxWrapper.appendChild(box);
      }

      //   codeforces block
      else if (this.friendlist[i]["platform"] == "codeforces") {
   
            await axios.get('https://competeapi.vercel.app/user/codeforces/'+this.friendlist[i]["username"])
        .then((res) => {
          // console.log(res.data);
          this.userdata = res.data;
          // console.log(this.userdata)
        })
        .catch(() => {
          // console.log(error);
          this.errmsg = "Error in fetching data";
        });

        const boxWrapper = document.getElementById("addrowcodeforces");
        const box = document.createElement("tr");
        box.innerHTML = `
                <td>${this.userdata[0]["firstName"]}</td>
              <td>${this.friendlist[i]["username"]}</td>
              <td>${this.userdata[0].rating}</td>
              <td>${this.userdata[0].maxRating}</td>
              <td>${this.userdata[0].rank}</td>
              <td>${this.userdata[0].maxRank}</td>
              <td>${this.userdata[0].contribution}</td>
      `;
        boxWrapper.appendChild(box);
      }

      //    leetcode block
      else if (this.friendlist[i]["platform"] == "leetcode") {
      
            await axios.get("api/leetcode/user/" +this.friendlist[i]["username"])
        .then((response) => {
                    console.log(response.data);
                this.posts2=response.data['data']
                console.log(this.contestRanking=this.posts2['userContestRanking'])
                this.toatal_solved=this.posts2['matchedUser']['submitStats']['acSubmissionNum'][0].count
                this.easy_solved=this.posts2['matchedUser']['submitStats']['acSubmissionNum'][1].count
                this.medium_solved=this.posts2['matchedUser']['submitStats']['acSubmissionNum'][2].count
                this.hard_solved=this.posts2['matchedUser']['submitStats']['acSubmissionNum'][3].count
        })
        .catch((error) => {
          console.log(error);
          this.errmsg = "Error in fetching data";
        });

        const boxWrapper = document.getElementById("addrowleetcode");

        const box = document.createElement("tr");
        box.innerHTML = `
              <td>${this.friendlist[i]["username"]}</td>
          
              <td>${this.toatal_solved}</td>
         
              <td>${this.easy_solved}</td>
              <td>${this.medium_solved}</td>
              <td>${this.hard_solved}</td>
      `;
        boxWrapper.appendChild(box);
      }
    }
  },
};
</script>




<style scoped>

.freindinputCont {
  display: flex;
  justify-content: center;
  align-content: center;
  /* border: 2px solid black; */
}
.freindinputbox {
  height: 150px;
  display: flex;
  justify-content: center;
  align-content: center;
  border: 2px solid gray;
  background: #bdc3c7;
  background: -webkit-linear-gradient(to right, #536577, #bdc3c7);
  background: linear-gradient(to right, #536577, #bdc3c7);
  padding: 90px;
  border-radius: 10px;
}
#selectinput {
  margin-bottom: 20px;
}

.outputCont {
    margin-top: 50px;
  min-height: 450px;
  /* border: 2px solid black; */
}
.platform {
  width: 85%;
  /* border: 2px solid red; */
  padding: 25px;
  margin: auto;
}
.heading {
  font-size: 20px;
  background: #bdc3c7; 
  background: -webkit-linear-gradient(
    to right,
    #536577,
    #bdc3c7
  ); 
  background: linear-gradient(
    to right,
    #536577,
    #bdc3c7
  );

  color: white;
  letter-spacing: 1px;
  font-weight: 600px;
}
</style>