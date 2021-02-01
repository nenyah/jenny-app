<template>
    <view>
        <view class=" h-full flex flex-col items-center">
            <image
                class="w-32 h-32 rounded-full bg-red-500 text-gray-100 flex justify-center items-center text-lg mt-20"
                src="@/static/logo.png"
            ></image
            >
            <view>提醒工具</view>
            <button
                v-if="!AuthorizedUserInfo"
                class="text-gray-100 bg-green-400 px-2 mt-5 rounded-full border-none border-0 border-gray-100 overflow-hidden"
                open-type="getUserInfo"
                @getuserinfo="getuserinfo"
            >
                授权使用微信头像、昵称
            </button>
            <button
                v-if="AuthorizedUserInfo && !AuthorizedPhone"
                class="text-gray-100 bg-green-400 px-2 mt-5 rounded-full border-none border-0 border-gray-100 overflow-hidden"
                open-type="getPhoneNumber"
                @getphonenumber="getphonenumber"
            >
                微信一键登录
            </button>
        </view>
    </view>
</template>

<script lang="ts">
import {Component, Vue, Watch} from "vue-property-decorator"
import api from "@/api"
type Code = string
type Signature = string
type EncryptedData = string
type IV = string
interface UserParams {
    signature: Signature
    encryptedData: EncryptedData
    iv: IV
}
@Component({})
export default class Login extends Vue {
    [x: string]: any
    private AuthorizedUserInfo = false
    private AuthorizedPhone = false
    @Watch("AuthorizedPhone")
    finishPhone(newValue: string, oldValue: string) {
        uni.navigateBack({})
    }
    isFail(e: any) {
        return e.detail.errMsg.includes("fail")
    }
    showFailMsg() {
        uni.showToast({
            title: "为了更好的为你服务，请同意授权",
            icon: "none",
        })
    }
    async getphonenumber(e: any) {
        console.log("获取手机触发", e.detail)
        if (this.isFail(e)) {
            console.log("获取授权失败")
            this.showFailMsg()
            return
        }
        this.AuthorizedPhone = true
        let postParams = e.detail as UserParams
        await api.user.info({
            signature: postParams.signature,
            encryptedData: postParams.encryptedData,
            iv: postParams.iv,
        })
        this.$store.commit("SET_IS_LOGIN", true)
        await this.$store.dispatch("user/login")
    }
    async getuserinfo(e: any) {
        console.log("获取用户信息触发", e.detail)
        if (this.isFail(e)) {
            console.log("获取授权失败")
            this.showFailMsg()
            return
        }
        this.AuthorizedUserInfo = true
        let postParams = e.detail as UserParams
        await api.user.info({
            signature: postParams.signature,
            encryptedData: postParams.encryptedData,
            iv: postParams.iv,
        })
    }
}
