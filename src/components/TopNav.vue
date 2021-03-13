<template>
  <div id="nav" :class="( modalActive ? 'transparent' : '' )">
    <div class="logo"></div>
    <div class="center">
        <router-link to="/"><div class="icon icon1"></div>Overview</router-link>
        <!--router-link to="/training-schedule"><div class="icon icon2"></div>Training Schedule</router-link-->
        <router-link to="/performance-analysis"><div class="icon icon3"></div>Performance Analysis</router-link>
        <router-link to="/skills-analysis"><div class="icon icon4"></div>Skills Analysis</router-link>
        <router-link to="/team-organization"><div class="icon icon5"></div>Team Organization</router-link>
        <router-link to="/employee-analysis"><div class="icon icon6"></div>Employee Analysis</router-link>
    </div>
    <div class="right">
        <div @click="openModal('Notifications')" class="notifications-container"><div v-if="notificationCount >= 1" class="badge">{{ notificationCount }}</div></div>
        <div @click="openModal('Exports')" class="exports-link">Exports</div>
    </div>
  </div>
</template>

<script>
import store from '../store';
import { dbUtils } from '../mixins/dbUtils';

export default {
    name: 'TopNav',
    mixins: [dbUtils],
    computed: {
        modalActive() {
            return store.state.modalActive;
        },
        modalContext() {
            return store.state.modalContext;
        },
        notificationCount() {
            return store.state.notifications.length;
        }
    }
}
</script>

<style lang="scss" scoped>
@import '../assets/styles/global';

.badge {
    background: red;
    color: white;
    height: 16px;
    width: 16px;
    border-radius: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    position: absolute;
    transform: translate(12px, -2px);
}

.exports-link {
    background: $colorMain;
    color: white;
    padding: $btnPad;
    border-radius: $rad;
    transition: 200ms;
    cursor: pointer;

    &:hover {
        transform: $shrink;
    }
}

.transparent {
    background: transparent !important;
}

.notifications-container {
    height: 24px;
    width: 24px;
    margin-right: 18px;
    background-image: url('https://raw.githubusercontent.com/tbrew1023/icommon/243c2c6c8080228d0b69ed7aa57f2bb0bdb4ded0/notify.svg');
    background-size: contain;
    background-repeat: no-repeat;
    background-position: center;
    transition: $quick;
    cursor: pointer;
    filter: brightness(0);

    &:hover {
        transform: $shrink;
    }
}

.profile-container {
    display: flex;
    justify-content: flex-end;
    align-items: center;

    .profile-img {
        height: 32px;
        width: 32px;
        background: black;
        margin-right: 12px;
        background-size: 130%;
        border-radius: 100%;
        background-position: center;
        background-repeat: no-repeat;
        background-image: url('https://github.com/tbrew1023/icommon/blob/master/images/skaterboi.png?raw=true');
    }
}

.logo, .right {
    width: 500px;
}

.right {
    display: flex;
    align-items: center;
    justify-content: flex-end;
    margin-right: 24px;
    font-weight: bold;
    font-size: 14px;
}

.logo {
    height: 42px;
    background-image: url('../assets/leadershipwise-logo4.png');
    background-position: left;
    background-size: contain;
    background-repeat: no-repeat;
    margin-left: 24px;
}

.center {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
}

#nav {
  padding: 0px;
  margin-top: 24px;
  position: absolute;
  top: 0px;
  display: flex;
  justify-content: space-between;
  width: 100%;
  background: white;
  height: 80px;
  align-items: center;
  margin: 0px;
  z-index: 99999;
  transition: 300ms;

  a {
    font-size: 14px;
    text-decoration: none;
    padding: 12px 18px;
    border-radius: 36px;
    margin-left: 12px;
    margin-right: 12px;
    color: rgba(black, 0.3);
    font-weight: bold;
    transition: 200ms;
    display: flex;
    align-items: center;

    &:hover {
        background: #eee;
    }

    &.router-link-exact-active {
      color: $colorMain;
      opacity: 1;
    }
  }
}

.ghost-inactive {
    background: transparent !important;
    color: black !important;
}

.icon {
    //background: black;
    height: 24px;
    width: 24px;
    margin-right: 18px;
    background-size: contain;
    background-repeat: no-repeat;
    background-position: center;
}

.icon1 {
    background-image: url('../assets/icons/dashboard.svg');
}

.icon2 {
    background-image: url('../assets/icons/schedule.svg');
}

.icon3 {
    background-image: url('../assets/icons/performance-analysis.svg');
}

.icon4 {
    background-image: url('../assets/icons/monthly-stats.svg');
}

.icon5 {
    background-image: url('../assets/icons/team-organization.svg');
}

.icon6 {
    background-image: url('../assets/icons/results.svg');
}

.icon7 {
    background-image: url('../assets/icons/employee-analysis.svg');
}

.router-link-exact-active {
    background: #22C4AB;
    color: white !important;

    &:hover {
        background: #22C4AB !important;
        opacity: 0.5 !important;
    }

    .icon {
        filter: brightness(0) invert(1);
    }
}
</style>