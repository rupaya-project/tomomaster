<template>
    <div id="app">
        <div class="page-layout">
            <b-navbar
                toggleable="lg"
                type="dark"
                variant="info">
                <div class="container">
                    <b-navbar-brand to="/">
                        <img src="/app/assets/img/logo.svg" >
                    </b-navbar-brand>
                    <b-navbar-toggle
                        target="nav-collapse"
                        class="btn-menu-sp"/>
                    <b-collapse
                        id="nav-collapse"
                        is-nav>
                        <!-- Right aligned nav items -->
                        <b-navbar-nav class="ml-auto ">
                            <b-nav-form class="search-form">
                                <auto-complete
                                    v-model="search"
                                    :items="items"/>
                                <b-button
                                    variant="outline-success"
                                    type="submit"
                                    @click="searchCandidate">Search</b-button>
                            </b-nav-form>
                        </b-navbar-nav>
                        <b-navbar-nav class="ml-auto navbar-buttons">
                            <b-button
                                v-if="!isRupxnet"
                                id="btn-become-candidate"
                                to="/setting"
                                variant="primary">Login</b-button>
                            <b-button
                                v-else
                                id="btn-become-candidate"
                                to="/apply"
                                variant="primary">Become a candidate</b-button>
                            <b-dropdown
                                class="dd-setting ml-3"
                                right
                                offset="25"
                                no-caret
                                variant="link">
                                <template
                                    slot="button-content">
                                    <i
                                        class="tm-bell icon-2x"
                                        @click="readClick" />
                                    <span
                                        :class="`notification__bell rupaya-status-dot rupaya-status-dot--yellow`"
                                        :style="(isRupxnet ? (readNoti <= 0 ? 'display: none;': '') : statusClass)"/>
                                </template>
                                <b-dropdown-text>
                                    <div class="notification_header">
                                        <span class="float-left">Notifications</span>
                                        <b-button
                                            variant="primary"
                                            class="float-right readAllBtn"
                                            @click="markReadAll">Mark all as read</b-button>
                                    </div>
                                </b-dropdown-text>
                                <b-dropdown-divider />
                                <div
                                    v-if="isRupxnet"
                                    class="notification_body">
                                    <div
                                        v-if="notifications.length <= 0"
                                        class="notification_body__empty">
                                        No notifications.</div>
                                    <div
                                        v-for="(value, key) in notifications"
                                        :key="key">
                                        <b-dropdown-text v-if="value.event === 'Slash'">
                                            <div>
                                                <span
                                                    :style="value.isRead ? '' :
                                                    'font-weight: bold;'"
                                                    class="notification_body__content">
                                                    <span class="notification_label slashed">Slashed</span>
                                                    Masternode
                                                    [<router-link :to="`/candidate/${value.candidate}`">
                                                        <span class="masternode-name">{{ value.name }}</span>
                                                    </router-link>]
                                                    has been slashed
                                                </span>
                                                <div class="notification_body__time">RupayaGov -
                                                    {{ value.createdAt }}</div>
                                            </div>
                                        </b-dropdown-text>
                                        <b-dropdown-divider
                                            v-if="value.event === 'Slash' &&
                                            key !== notifications.length - 1"/>

                                        <b-dropdown-text v-if="value.event === 'Outtop'">
                                            <div>
                                                <span
                                                    :style="value.isRead ? '' :
                                                    'font-weight: bold;'"
                                                    class="notification__content">
                                                    <span class="notification_label outtop">Out top</span>
                                                    Masternode [<router-link :to="`/candidate/${value.candidate}`">
                                                        <span class="masternode-name">{{ value.name }}</span>
                                                    </router-link>]
                                                    left the top 150 and is no longer a masternode.
                                                </span>
                                                <div class="notification__time">RupayaGov -
                                                    {{ value.createdAt }}</div>
                                            </div>
                                        </b-dropdown-text>
                                        <b-dropdown-divider
                                            v-if="value.event === 'Outtop' &&
                                            key !== notifications.length - 1"/>

                                        <b-dropdown-text v-if="value.event === 'Propose'">
                                            <div>
                                                <span
                                                    :style="value.isRead ? '' :
                                                    'font-weight: bold;'"
                                                    class="notification__content">
                                                    <span class="notification_label proposed">Proposed</span>
                                                    Congratulation for having your own new candidate!
                                                    Its time to gather votes from community by promoting it and
                                                    be in top 150 to get your first reward.
                                                </span>
                                                <div class="notification__time">RupayaGov -
                                                    {{ value.createdAt }}</div>
                                            </div>
                                        </b-dropdown-text>
                                        <b-dropdown-divider
                                            v-if="value.event === 'Propose' &&
                                            key !== notifications.length - 1"/>

                                        <b-dropdown-text v-if="value.event === 'Resign'">
                                            <div>
                                                <span
                                                    :style="value.isRead ? '' :
                                                    'font-weight: bold;'"
                                                    class="notification__content">
                                                    <span class="notification_label resigned">Resigned</span>
                                                    Masternode [<router-link :to="`/candidate/${value.candidate}`">
                                                        <span class="masternode-name">{{ value.name }}</span>
                                                    </router-link>] resigned
                                                </span>
                                                <div class="notification__time">RupayaGov -
                                                    {{ value.createdAt }}</div>
                                            </div>
                                        </b-dropdown-text>
                                        <b-dropdown-divider
                                            v-if="value.event === 'Resign' &&
                                            key !== notifications.length - 1"/>
                                        <b-dropdown-text v-if="value.event === 'Withdraw'">
                                            <div>
                                                <span
                                                    :style="value.isRead ? '' :
                                                    'font-weight: bold;'"
                                                    class="notification__content">
                                                    <span class="notification_label withdraw">Withdraw</span>
                                                    {{ value.amount }} unvoted RUPX are ready to withdraw
                                                </span>
                                                <div class="notification__time">RupayaGov -
                                                    {{ value.createdAt }}</div>
                                            </div>
                                        </b-dropdown-text>
                                        <b-dropdown-divider
                                            v-if="value.event === 'Withdraw' &&
                                            key !== notifications.length - 1"/>
                                    </div>
                                </div>
                                <div
                                    v-if="!isRupxnet"
                                    class="notification_body">
                                    <b-dropdown-text>
                                        <div style="font-size: 13px;">
                                            <strong>RupayaGov up to 1.6.4</strong>
                                            <p>- Support pantograph</p>
                                        </div>
                                    </b-dropdown-text>
                                    <b-dropdown-divider/>
                                    <b-dropdown-text>
                                        <div style="font-size: 13px;">
                                            <strong>RupayaGov up to 1.6.3</strong>
                                            <p>- Remove masternode balance</p>
                                            <p>- Display masternode name in reward table</p>
                                            <p>- Improve loading reward table</p>
                                            <p>- Update new logo</p>
                                        </div>
                                    </b-dropdown-text>
                                    <b-dropdown-divider/>
                                    <b-dropdown-text>
                                        <div style="font-size: 13px;">
                                            <strong>RupayaGov up to 1.6.2</strong>
                                            <p>- Fix test api issue</p>
                                        </div>
                                    </b-dropdown-text>
                                    <b-dropdown-divider/>
                                    <b-dropdown-text>
                                        <div style="font-size: 13px;">
                                            <strong>RupayaGov up to 1.6.1</strong>
                                            <p>- Add check owner address</p>
                                            <p>- Clean unused libraries</p>
                                            <p>- Update webpack</p>
                                        </div>
                                    </b-dropdown-text>
                                    <b-dropdown-divider/>
                                    <b-dropdown-text>
                                        <div style="font-size: 13px;">
                                            <strong>RupayaGov up to 1.6.0</strong>
                                            <p>- Add Auto tweeting when a new candidate is proposed</p>
                                            <p>- Update searching</p>
                                            <p>- Add top page pagination on small screen</p>
                                            <p>- Remove hardware wallet session storing</p>
                                        </div>
                                    </b-dropdown-text>
                                    <b-dropdown-divider/>
                                    <b-dropdown-text>
                                        <div style="font-size: 13px;">
                                            <strong>RupayaGov up to 1.5.2</strong>
                                            <p>- Hide latest signed block for proposed masternode</p>
                                            <p>- Refactor code</p>
                                        </div>
                                    </b-dropdown-text>
                                    <b-dropdown-divider/>
                                    <b-dropdown-text>
                                        <div style="font-size: 13px;">
                                            <strong>RupayaGov up to 1.5.1</strong>
                                            <p>- Hide staking ROI if a masternode out of top 150</p>
                                            <p>- Fix missing latest reward record</p>
                                        </div>
                                    </b-dropdown-text>
                                    <b-dropdown-divider/>
                                    <b-dropdown-text>
                                        <div style="font-size: 13px;">
                                            <strong>RupayaGov up to 1.5.0</strong>
                                            <p>- Display average ROI for masternode owner and voter</p>
                                            <p>- Display owner ROI and voter ROI in masternode detail page</p>
                                            <p>- Add withdrawal notification</p>
                                            <p>- Add sitemap</p>
                                        </div>
                                    </b-dropdown-text>
                                </div>
                                <b-dropdown-divider
                                    v-if="!isRupxnet" />
                                <b-dropdown-text
                                    v-if="!isRupxnet"
                                    class="notification_bottom">
                                    RupayaGov - {{ version }}
                                </b-dropdown-text>
                            </b-dropdown>
                            <b-dropdown
                                v-if="isRupxnet"
                                class="dd-setting ml-1"
                                right
                                offset="25"
                                no-caret
                                variant="primary">
                                <template
                                    slot="button-content">
                                    <i class="tm-cog icon-2x"/>
                                </template>
                                <b-dropdown-item
                                    :to="`/voter/${account}`"
                                    class="dd-address">
                                    {{ truncate(account, 20) }}
                                </b-dropdown-item>
                                <b-dropdown-divider />
                                <b-dropdown-item
                                    target="_bank"
                                    href="https://bit.ly/2B6p29o">Help</b-dropdown-item>
                                <b-dropdown-item to="/setting">Settings/Withdraws</b-dropdown-item>
                                <b-dropdown-divider />
                                <b-dropdown-item
                                    v-if="!mobileCheck && isRupxnet"
                                    href="/"
                                    @click="signOut">Sign out</b-dropdown-item>
                            </b-dropdown>
                        </b-navbar-nav>
                    </b-collapse>
                </div>
            </b-navbar>
            <div class="main-content">
                <router-view/>
            </div>
            <footer
                class="rupaya-footer">
                <div class="container">
                    <div class="row">
                        <div class="col-md-8">
                            <div class="rupaya-footer__copyright">
                                RupayaGov &copy; {{ (new Date()).getFullYear() }} -
                                <a
                                    :href="`https://github.com/rupaya-project/gov/releases/tag/v${version}`"
                                    class="version-tag">
                                    v{{ version }}</a>
                            </div>
                            <div class="rupaya-footer__links">
                                <ul class="list-inline">
                                    <li class="list-inline-item">
                                        <a
                                            :href="needHelpLink"
                                            target="_blank">
                                        <i class="tm-lifebuoy mr-1"/>Need help?</a>
                                    </li>
                                    <li class="list-inline-item">
                                        <a
                                            target="_blank"
                                            href="https://docs.rupx.io/legal/privacy">
                                        <i class="tm-lock mr-1"/>Privacy Policy</a>
                                    </li>
                                    <li class="list-inline-item">
                                        <a
                                            target="_blank"
                                            href="https://docs.rupx.io/legal/terms-of-use">
                                        <i class="tm-profile mr-1"/>Terms of Use</a>
                                    </li>
                                    <li class="list-inline-item">
                                        <a
                                            target="_blank"
                                            href="/api-docs"><i class="tm-checklist mr-1"/>API Documentation</a>
                                    </li>
                                </ul>
                            </div>
                        </div>
                        <div class="col-md-4 rupaya-footer__social">
                            <ul class="list-inline">
                                <li class="list-inline-item">
                                    <a
                                        href="https://t.me/rupayachain"
                                        target="_blank">
                                        <i class="tm-telegram" />
                                    </a>
                                </li>
                                <li class="list-inline-item">
                                    <a
                                        href="https://www.facebook.com/rupayachainofficial"
                                        target="_blank">
                                        <i class="tm-facebook" />
                                    </a>
                                </li>
                                <li class="list-inline-item">
                                    <a
                                        href="https://twitter.com/RupayaANN"
                                        target="_blank">
                                        <i class="tm-twitter" />
                                    </a>
                                </li>
                                <li class="list-inline-item">
                                    <a
                                        href="https://github.com/rupayachain/"
                                        target="_blank">
                                        <i class="tm-github" />
                                    </a>
                                </li>
                                <li class="list-inline-item">
                                    <a
                                        href="https://www.reddit.com/r/Rupaya/"
                                        target="_blank">
                                        <i class="tm-reddit" />
                                    </a>
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>
            </footer>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import store from 'store'
import moment from 'moment'
import pkg from '../package.json'
import AutoComplete from './components/AutoComplete.vue'
export default {
    name: 'App',
    metaInfo: {
        title: 'Rupaya Governance DApp | RupayaGov',
        meta: [
            { name: 'description', content: 'Rupaya Gov - Providing a professional UI which allows coin-holders to stake for masternodes, decentralized governance and explore masternode performance statistics' } // eslint-disable-line
        ]
    },
    components: {
        AutoComplete
    },
    data () {
        return {
            isReady: !!this.web3,
            showProgressBar: false,
            selectedCandidate: null,
            search: null,
            isRupxnet: false,
            version: pkg.version,
            account: '',
            items: [],
            statusClass: '',
            interval: '',
            notifications: [],
            readNoti: 0,
            needHelpLink: 'https://docs.rupx.io/faq/products/rupayachain-applications/gov'
        }
    },
    computed: {
        mobileCheck: () => {
            const isAndroid = navigator.userAgent.match(/Android/i)
            const isIOS = navigator.userAgent.match(/iPhone|iPad|iPod/i)
            return (isAndroid || isIOS)
        }
    },
    async updated () {
        await this.checkNetworkAndLogin()
    },
    destroyed () {
        if (this.interval) {
            clearInterval(this.interval)
        }
    },
    created: async function () {
        let self = this

        try {
            if (!self.isReady && self.NetworkProvider === 'metamask') {
                throw Error('Web3 is not properly detected. Have you installed MetaMask extension?')
            }
            self.$bus.$on('logged', async () => {
                await self.checkNetworkAndLogin()
                setTimeout(async () => {
                    await self.getNotification()
                }, 500)
            })
            // const candidates = await axios.get('/api/candidates')
            // const map = candidates.data.items.map((c) => {
            //     return {
            //         name: c.name ? c.name : 'Anonymous',
            //         address: c.candidate
            //     }
            // })
            // const mapping = await Promise.all(map)
            // self.items = mapping
            setTimeout(async () => {
                await self.getNotification()
            }, 500)
            if (this.isRupxnet) {
                this.interval = setInterval(async () => {
                    await this.getNotification()
                }, 40000)
            }
        } catch (e) {
            console.log(e)
        }
    },
    methods: {
        searchCandidate (e) {
            e.preventDefault()
            const regexpAddr = /^(0x)?[0-9a-fA-F]{40}$/

            let to = null
            let search = (this.search || '').trim()
            if (regexpAddr.test(search)) {
                axios.get(`/api/search/${search}`)
                    .then((response) => {
                        const data = response.data
                        if (Object.keys(data.candidate).length > 0) {
                            to = { path: `/candidate/${data.candidate.candidate}` }
                        } else if (Object.keys(data.voter).length > 0) {
                            to = { path: `/voter/${search}` }
                        } else {
                            this.$toasted.show('Not found')
                        }
                        if (!to) {
                            return false
                        }
                        this.search = ''
                        return this.$router.push(to)
                    }).catch(e => console.log(e))
            }
        },
        goPage: function (s) {
            this.$router.push({ path: `/candidate/${s}` })
        },
        async checkNetworkAndLogin () {
            let self = this
            setTimeout(async () => {
                try {
                    let contract// = await self.getRupayaValidatorInstance()
                    contract = self.RupayaValidator
                    self.account = store.get('address') ||
                        self.$store.state.address || await self.getAccount()
                    if (self.account && contract) {
                        self.isRupxnet = true
                    }
                } catch (error) {}
            }, 0)
        },
        signOut () {
            store.clearAll()
            Object.assign(this.$store.state, this.getDefaultState())
            // this.$store.state.address = null

            this.$router.go({
                path: '/'
            })
        },
        async readClick () {
            this.statusClass = 'display: none;'
        },
        async getNotification () {
            try {
                const self = this
                if (self.account && self.isRupxnet) {
                    const { data } = await axios.get('/api/voters/' + self.account.toLowerCase() + '/getNotification')
                    if (data.length > 0) {
                        let items = []
                        let readNoti = 0
                        data.map(d => {
                            if (!d.isRead) {
                                readNoti++
                            }
                            items.push({
                                event: d.event,
                                createdAt: moment(d.createdAt).fromNow(),
                                name: d.candidateName,
                                candidate: d.candidate,
                                isRead: d.isRead
                            })
                        })
                        self.readNoti = readNoti
                        self.notifications = items
                    }
                }
            } catch (error) {
                console.log(error)
            }
        },
        async markReadAll () {
            // mark read all
            this.readNoti = 0
            await axios.get('/api/voters/' + this.account.toLowerCase() + '/markReadAll')
            this.notifications = this.notifications.map(n => {
                n.isRead = true
                return n
            })
        }
    }
}
</script>
