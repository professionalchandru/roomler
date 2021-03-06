<template>
  <v-menu
    v-model="menu"
    open-on-hover
    left
    offset-x
  >
    <template v-slot:activator="{ on }">
      <v-btn
        v-if="isTransferVisible || isToMemberVisible || isToModeratorVisible || isRemoveVisible"
        :disabled="!isTransferVisible && !isToMemberVisible && !isToModeratorVisible && !isRemoveVisible"
        fab
        small
        text
        dark
        v-on="on"
      >
        <v-icon>fa-edit</v-icon>
      </v-btn>
    </template>
    <v-list v-if="isTransferVisible || isToMemberVisible || isToModeratorVisible || isRemoveVisible">
      <v-list-item
        v-if="isTransferVisible"
        @click="openTransfer()"
      >
        <v-list-item-title>
          Transfer Ownership
        </v-list-item-title>
      </v-list-item>
      <v-divider :if="isTransferVisible" />
      <v-list-item
        v-if="isToMemberVisible"
        @click="toMember()"
      >
        <v-list-item-title>
          Demote to Member
        </v-list-item-title>
      </v-list-item>
      <v-divider v-if="isToMemberVisible" />
      <v-list-item
        v-if="isToModeratorVisible"
        @click="toModerator()"
      >
        <v-list-item-title>
          Promote to Moderator
        </v-list-item-title>
      </v-list-item>
      <v-divider v-if="isToModeratorVisible" />
      <v-list-item
        v-if="isRemoveVisible"
        @click="openPeerDelete()"
      >
        <v-list-item-title>
          Remove Peer
        </v-list-item-title>
      </v-list-item>
      <v-divider v-if="isRemoveVisible" />
    </v-list>
  </v-menu>
</template>

<script>

export default {
  props: {
    room: {
      type: Object,
      default: null
    },
    user: {
      type: Object,
      default: null
    },
    currentUser: {
      type: Object,
      default: null
    },
    role: {
      type: String,
      default: null
    },
    currentRole: {
      type: String,
      default: null
    }
  },
  data () {
    return {
      menu: false
    }
  },
  computed: {
    isToMemberVisible () {
      return this.currentUser &&
        this.user &&
        this.currentUser._id !== this.user._id &&
        this.currentRole === 'owner' &&
        this.role === 'moderator'
    },
    isToModeratorVisible () {
      return this.currentUser &&
        this.user &&
        this.currentUser._id !== this.user._id &&
        (this.currentRole === 'owner' || this.currentRole === 'moderator') &&
        this.role === 'member'
    },
    isTransferVisible () {
      return this.currentUser &&
        this.user &&
        this.currentUser._id !== this.user._id &&
        this.currentRole === 'owner'
    },
    isRemoveVisible () {
      return this.currentUser &&
        this.user &&
        this.currentUser._id !== this.user._id &&
        (this.currentRole === 'owner' || (this.currentRole === 'moderator' && this.role === 'member'))
    }
  },
  methods: {
    toMember () {
      this.$emit('toMember', this.user)
    },
    toModerator () {
      this.$emit('toModerator', this.user)
    },
    openPeerDelete () {
      this.$emit('openPeerDelete', this.user, this.currentRole)
    },
    openTransfer () {
      this.$emit('openTransfer', this.user)
    }
  }
}
</script>
