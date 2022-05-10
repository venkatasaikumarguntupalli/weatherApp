<template>
<div>
    <form class="signup-form" action="/register" method="post">
        <div class="form-header">
            <h1>Add Data</h1>
        </div>
        
        <div class="form-body">
            <div class="horizontal-group">
                <div v-if="errors.length">
                <h5 v-for="error in errors" :key="error">{{error}}</h5>
            </div>
                <div class="form-group left">
                    <label for="firstname" class="label-title">City *</label>
                    <input type="text" id="firstname" class="form-input" placeholder="Enter city" required="required" v-model="city" />
                </div>
                <div class="form-group right">
                    <label for="lastname" class="label-title">Time *</label>
                    <input type="text" id="lastname" class="form-input" placeholder="Enter time" v-model="time" />
                </div>
            </div>
            <div class="form-group">
                <label for="email" class="label-title"> Temperature *</label>
                <input type="email" id="email" class="form-input" placeholder="Enter Temperature" required="required" v-model="temperature" />
            </div>
            <div class="form-group">
                <label for="phone" class="label-title">Humidity *</label>
                <input type="phone" id="phone" class="form-input" placeholder="Enter Humidity" required="required" v-model="humidity" />
            </div>
            <!-- <div class="horizontal-group">
                <div class="form-group">
                    <label for="account" class="label-title">Account Type :
                    </label>

                    <input type="radio" id="account" name="account" value="Public" v-model="security" />Public
                    <input type="radio" id="account" name="account" value="Private" v-model="security" />Private
                </div>
            </div> -->
            <div class="horizontal-group">

                <div class="form-group right">
                    <label for="date" class="label-title"> Date *</label>
                    <input type="date" id="date" class="form-input" placeholder="dd/mm/yyyy" required="required" v-model="date" />
                </div>
            </div>
            <div class="horizontal-group">
                <div class="form-group left">
                    <label for="password" class="label-title">Pressure *</label>
                    <input type="text" id="password" class="form-input" placeholder="Enter Pressure" required="required" v-model="pressure" />
                </div>
            </div>
        </div>

        <div class="form-footer">
            <button type="submit" @click.prevent="postData" method="post" class="btn">submit</button><br>
        </div>
    </form>
</div>
</template>

<script>
export default {
    name: "AddData",
    data() {
        return {

            city: '',
            time: '',
            temperature: '',
            humidity: '',
            pressure: '',
            date: '',
            errors:[]

        }
    },
    methods: {
        processFile(event) {
            this.someData = event.target.files[0]
        },
        handleFileUpload(event) {
            this.file = event.target.files;
        },

        postData() {
            this.errors = [];
            if (!this.city) {
                this.errors.push("Please enter city")
            } else if (!this.time) {
                this.errors.push("Please enter time")
            } else if (!this.temperature) {
                this.errors.push("Please enter temperature")
            } else if (!this.humidity) {
                this.errors.push("Please enter humidity")
            } else if (!this.pressure) {
                this.errors.push("Please enter pressure")
            } else if (!this.date) {
                this.errors.push("Please enter date")
            }

            if (this.errors.length == 0) {
                this.axios.post(`http://localhost:8083/addData`, {
                        city: this.city,
                        time: this.time,
                        temprature: this.temprature,
                        humidity: this.humidity,
                        pressure: this.pressure,
                        date: this.date

                    })
                    .then((result) => {
                        console.log("Result", result)
                    })
            }

        }
    }

};
</script>

<style scoped>
.signup-form {
    font-family: sans-serif;
    width: 600px;
    margin: 30px auto;
    /* background: linear-gradient(to right, #ffffff 0%, #fafafa 50%, #ffffff 99%); */
    border-radius: 10px;
}

.form-header {
    /* background-color: #eff0f1; */
    background-color: rgb(48, 155, 197);
    border-top-left-radius: 10px;
    border-top-right-radius: 10px;
}

.form-header h1 {
    font-size: 30px;
    text-align: center;
    /* color: #666; */
    color: white;
    padding: 20px 0;
    border-bottom: 1px solid #cccccc;
}

.form-body {
    padding: 10px 40px;
    text-align: left;
}

.form-group {
    margin-bottom: 20px;
}

.form-body .label-title {
    color: #1bb5ba;
    font-size: 17px;
    text-align: left;
}

.form-body .form-input {
    font-size: 17px;
    box-sizing: border-box;
    width: 100%;
    height: 34px;
    padding-left: 10px;
    padding-right: 10px;
    color: #333333;
    text-align: left;
    border: 1px solid #d6d6d6;
    border-radius: 4px;
    background: white;
    outline: none;
}

.horizontal-group .left {
    float: left;
    width: 49%;
}

.horizontal-group .right {
    float: right;
    width: 49%;
}

input[type="file"] {
    outline: none;
    cursor: pointer;
    font-size: 17px;
}

::-webkit-input-placeholder {
    color: #d9d9d9;
}

.form-footer {
    clear: both;

}

.signup-form {
    box-shadow: 0 2px 4px rgb(0 0 0 / 10%), 0 8px 16px rgb(0 0 0 / 10%);
    box-sizing: border-box;
}

.signup-form .form-footer {
    background-color: #eff0f1;
    border-bottom-left-radius: 10px;
    border-bottom-right-radius: 10px;
    padding: 10px 40px;
    text-align: center;
    border-top: 1px solid #cccccc;
}
@media only screen and (min-device-width: 150px) and (max-device-width: 300px) {
  .signup-form {
    display: flex;
    flex-direction: column;
    /* align-items: center; */
    width: 550px;
    padding: 100px;
  }
}
@media only screen and (min-device-width: 300px) and (max-device-width: 650px) {
  .signup-form {
    display: flex;
    flex-direction: column;
    width: 850px;
    padding-left: 250px;
  }
}

select {
    size: 400px;
    height: 34px;
    width: 100%;
    border-radius: 4px;
    text-align: center;
    font-size: 17px;
    border: 1px solid #d6d6d6;
}

.interests {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
}

#choose-file {
    border: none;
}

.btn {
    display: inline-block;
    padding: 10px 20px;
    background-color: #1ba2ba;
    font-size: 17px;
    border: none;
    border-radius: 5px;
    color: #bcf5e7;
    cursor: pointer;
}

.btn:hover {
    background-color: #169c7b;
    color: white;
}
</style>
