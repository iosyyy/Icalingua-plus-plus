<template>
    <div class="vac-room-header vac-app-border-b">
        <slot name="room-header" v-bind="{ room, typingUsers }">
            <div class="vac-room-wrapper">
                <div
                    v-if="!singleRoom"
                    class="vac-svg-button vac-toggle-button"
                    :class="{ 'vac-rotate-icon': !showRoomsList && !isMobile }"
                    @click="$emit('toggle-rooms-list')"
                >
                    <slot name="toggle-icon">
                        <svg-icon class="nonedrag" @click="$emit('room-menu')"
                                  @dblclick="$emit('open-group-member-panel')"
                                  name="toggle"/>
                    </slot>
                </div>
                <div
                    class="vac-info-wrapper"
                    :class="{ 'vac-item-clickable': roomInfo }"
                    @click="$emit('room-info', room)"
                >
                    <slot name="room-header-avatar" v-bind="{ room }">
                        <a @dblclick="$emit('pokefriend')">
                            <div
                                v-if="roomAvatar"
                                class="vac-room-avatar"
                                :style="{ 'background-image': `url('${roomAvatar}')` }"
                            />
                        </a>
                    </slot>
                    <slot name="room-header-info" v-bind="{ room, typingUsers, roomInfo }">
                        <div class="vac-text-ellipsis">
                            <div class="vac-room-name vac-text-ellipsis">
                                {{ room.roomName }}
                            </div>
                            <div
                                v-if="membersCount"
                                class="vac-room-info vac-text-ellipsis"
                            >
                                {{ membersCount }} 名成员
                            </div>
                        </div>
                    </slot>
                </div>
                <div class="nonedrag vac-svg-button vac-room-options">
                    <a v-on:click="min" autofocus="false">
                        <svg t="1670546305531" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
                             p-id="5411" width="20" height="20">
                            <path
                                d="M923 571H130.7c-27.6 0-50-22.4-50-50s22.4-50 50-50H923c27.6 0 50 22.4 50 50s-22.4 50-50 50z"
                                fill="#707070" p-id="5412"></path>
                        </svg>
                    </a>

                </div>
                <div style="margin-left: 20px" class="nonedrag vac-svg-button vac-room-options">
                    <a v-on:click="max">
                        <svg t="1670546396185" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
                             p-id="1042" width="20" height="20">
                            <path
                                d="M812.3 959.4H213.7c-81.6 0-148-66.4-148-148V212.9c0-81.6 66.4-148 148-148h598.5c81.6 0 148 66.4 148 148v598.5C960.3 893 893.9 959.4 812.3 959.4zM213.7 120.9c-50.7 0-92 41.3-92 92v598.5c0 50.7 41.3 92 92 92h598.5c50.7 0 92-41.3 92-92V212.9c0-50.7-41.3-92-92-92H213.7z"
                                fill="#707070" p-id="1043"></path>
                        </svg>
                    </a>
                </div>
                <div style="margin-left: 20px" class="nonedrag vac-svg-button vac-room-options">
                    <a v-on:click="close">
                        <svg t="1670545537363" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
                             p-id="2639" width="20" height="20">
                            <path
                                d="M453.44 512L161.472 220.032a41.408 41.408 0 0 1 58.56-58.56L512 453.44 803.968 161.472a41.408 41.408 0 0 1 58.56 58.56L570.56 512l291.968 291.968a41.408 41.408 0 0 1-58.56 58.56L512 570.56 220.032 862.528a41.408 41.408 0 0 1-58.56-58.56L453.44 512z"
                                fill="#707070" p-id="2640"></path>
                        </svg>
                    </a>
                </div>
            </div>
        </slot>
    </div>
</template>

<script>
import vClickOutside from 'v-click-outside'

import SvgIcon from '../../components/SvgIcon'

import typingText from '../../utils/typingText'
import getAvatarUrl from '../../../../../utils/getAvatarUrl'
import {ipcRenderer} from 'electron'

export default {
    name: 'RoomHeader',
    components: {
        SvgIcon,
    },

    directives: {
        clickOutside: vClickOutside.directive,
    },

    props: {
        currentUserId: {type: [String, Number], required: true},
        textMessages: {type: Object, required: true},
        singleRoom: {type: Boolean, required: true},
        showRoomsList: {type: Boolean, required: true},
        isMobile: {type: Boolean, required: true},
        roomInfo: {type: Function, default: null},
        menuActions: {type: Array, required: true},
        room: {type: Object, required: true},
        membersCount: {type: Number, default: 0},
    },

    data() {
        return {
            menuOpened: false,
        }
    },

    computed: {
        typingUsers() {
            return typingText(this.room, this.currentUserId, this.textMessages)
        },
        roomAvatar() {
            return getAvatarUrl(this.room.roomId)
        },
    },

    methods: {
        menuActionHandler(action) {
            this.closeMenu()
            this.$emit('menu-action-handler', action)
        },
        closeMenu() {
            this.menuOpened = false
        },
        min: function () {
            ipcRenderer.send('min')
        },
        max: function () {
            ipcRenderer.send('max')
        },
        close: function () {
            ipcRenderer.send('close')
        },
    },
}
</script>

<style lang="scss">
.vac-room-header {
    position: absolute;
    display: flex;
    align-items: center;
    height: 64px;
    width: 100%;
    z-index: 10;
    background: var(--chat-header-bg-color);
    border-top-right-radius: var(--chat-container-border-radius);
    -webkit-app-region: drag;
    -webkit-user-select: none;
}

.nonedrag {
    -webkit-app-region: no-drag;

}

.vac-room-wrapper {
    display: flex;
    align-items: center;
    min-width: 0;
    height: 100%;
    width: 100%;
    padding: 0 16px;
}

.vac-toggle-button {
    margin-right: 15px;

    svg {
        height: 26px;
        width: 26px;
    }
}

.vac-rotate-icon {
    transform: rotate(180deg) !important;
}

.vac-info-wrapper {
    display: flex;
    align-items: center;
    min-width: 0;
    width: 100%;
    height: 100%;
}

.vac-room-name {
    font-size: 17px;
    font-weight: 500;
    line-height: 22px;
    color: var(--chat-header-color-name);
}

.vac-room-info {
    font-size: 13px;
    line-height: 18px;
    color: var(--chat-header-color-info);
}

.vac-room-options {
    margin-left: auto;
}

@media only screen and (max-width: 768px) {
    .vac-room-header {
        height: 50px;

        .vac-room-wrapper {
            padding: 0 10px;
        }

        .vac-room-name {
            font-size: 16px;
            line-height: 22px;
        }

        .vac-room-info {
            font-size: 12px;
            line-height: 16px;
        }

        .vac-room-avatar {
            height: 37px;
            width: 37px;
            min-height: 37px;
            min-width: 37px;
        }
    }
}
</style>
