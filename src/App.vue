<template>
  <v-app>
    <v-app-bar app dark color="#092c4c">
      <v-toolbar-title><img src="/logo.svg" height="40" contain class="mr-3" /></v-toolbar-title>
    </v-app-bar>
    <v-container class="mt-16">
      <v-card class="pa-5 mb-5">
        <v-card-title class="text-h5">쿠버네티스 사전 설정</v-card-title>
        <v-divider class="mb-3"></v-divider>

        <SettingGroup title="CRI 설치">
          <SettingItem subtitle="containerd 설치">
            <v-btn color="primary">설치</v-btn>
          </SettingItem>
          <SettingItem subtitle="Cgroup 설정">
            <v-btn color="primary" @click="toggleBoolean('kubernetesPreconfiguration.CRI.cgroup')"
              >실행</v-btn
            >

            <StatusText
              :status="kubernetesPreconfiguration.CRI.cgroup"
              :text="
                kubernetesPreconfiguration.CRI.cgroup
                  ? '/etc/containerd/config.toml - SystemdCgroup=true'
                  : '/etc/containerd/config.toml - SystemdCgroup=false'
              "
            />
          </SettingItem>
        </SettingGroup>

        <SettingGroup title="리눅스 커널 모듈 설정">
          <SettingItem subtitle="">
            <v-btn color="primary" @click="toggleBoolean('kubernetesPreconfiguration.kernel')"
              >실행</v-btn
            >
            <StatusText
              :status="kubernetesPreconfiguration.kernel"
              :text="kubernetesPreconfiguration.kernel ? '활성화' : '비활성화'"
            />
          </SettingItem>
        </SettingGroup>

        <SettingGroup title="swap 비활성화">
          <SettingItem subtitle="">
            <v-btn @click="toggleBoolean('kubernetesPreconfiguration.swap')" color="primary"
              >실행</v-btn
            >
            <StatusText
              :status="kubernetesPreconfiguration.swap"
              :text="kubernetesPreconfiguration.swap ? 'swapoff' : 'swapon'"
            />
          </SettingItem>
        </SettingGroup>

        <SettingGroup title="방화벽 비활성화">
          <SettingItem subtitle="">
            <v-btn @click="toggleBoolean('kubernetesPreconfiguration.firewall')" color="primary"
              >실행</v-btn
            >
            <StatusText
              :status="kubernetesPreconfiguration.firewall"
              :text="kubernetesPreconfiguration.firewall ? '비활성화' : '활성화'"
            />
          </SettingItem>
        </SettingGroup>

        <SettingGroup title="SELinux 설정">
          <v-radio-group v-model="kubernetesPreconfiguration.SELinux" inline>
            <v-radio label="enforcing" value="enforcing"></v-radio>
            <v-radio label="permissive" value="permissive"></v-radio>
            <v-radio label="disable" value="disable"></v-radio>
          </v-radio-group>
        </SettingGroup>
      </v-card>

      <v-card class="pa-5 mb-5">
        <v-card-title class="text-h5">쿠버네티스 설치</v-card-title>
        <v-divider class="mb-3"></v-divider>

        <SettingGroup title="쿠버네티스 설치">
          <SettingItem subtitle="kubeadm, kubelet, kubectl">
            <v-btn color="primary">설치</v-btn>
          </SettingItem>
        </SettingGroup>

        <SettingGroup title="명령어 자동완성 설정">
          <SettingItem subtitle="">
            <v-btn color="primary">실행</v-btn>
          </SettingItem>
        </SettingGroup>
      </v-card>

      <v-card class="pa-5 mb-5">
        <v-card-title class="text-h5">쿠버네티스 구성</v-card-title>
        <v-divider class="mb-3"></v-divider>

        <SettingGroup title="마스터노드 구성">
          <SettingItem subtitle="Single, HA">
            <v-btn color="primary" @click="kubernetesConfigurationMasterNodeMode">실행</v-btn>
            <p class="text-caption mt-2">{{ kubernetesConfiguration.masterNode.mode }}</p>
          </SettingItem>

          <SettingItem subtitle="HA LB">
            <v-btn color="primary" @click="toggleBoolean('kubernetesConfiguration.masterNode.HALB')"
              >실행</v-btn
            >
            <StatusText
              :status="kubernetesConfiguration.masterNode.HALB"
              :text="
                kubernetesConfiguration.masterNode.HALB
                  ? 'HA LoadBalancer가 설정되었습니다.'
                  : 'HA LoadBalancer가 설정되지 않았습니다.'
              "
            />
          </SettingItem>
        </SettingGroup>

        <SettingGroup title="마스터노드 init 설정">
          <SettingItem subtitle="서비스 네트워크">
            <v-text-field
              width="200"
              hide-details
              variant="outlined"
              label="네트워크 입력"
              v-model="kubernetesConfiguration.masterNode.init.serviceNetwork"
              dense
            ></v-text-field>
          </SettingItem>
          <SettingItem subtitle="파드 네트워크 CIDR">
            <v-select
              width="200"
              v-model="kubernetesConfiguration.masterNode.init.podNetworkCIDR"
              :items="kubernetesConfiguration.masterNode.init.podNetworkCIDRList"
              label="CIDR 선택"
              variant="outlined"
              dense
              hide-details
            ></v-select>
          </SettingItem>
        </SettingGroup>

        <SettingGroup title="워커노드 추가">
          <SettingItem subtitle="">
            <v-btn
              color="primary"
              @click="toggleBoolean('kubernetesConfiguration.workerNode.add')"
              >{{ kubernetesConfiguration.workerNode.add ? '완료' : '실행' }}</v-btn
            >
          </SettingItem>
        </SettingGroup>

        <SettingGroup title="CNI 설치">
          <SettingItem subtitle="">
            <v-btn @click="toggleBoolean('kubernetesConfiguration.CNI')" color="primary">{{
              kubernetesConfiguration.CNI ? '완료' : '실행'
            }}</v-btn>
          </SettingItem>
        </SettingGroup>

        <SettingGroup title="kubectl 설정파일 설정">
          <SettingItem
            @click="toggleBoolean('kubernetesConfiguration.kubeconfig')"
            subtitle="KUBECONFIG"
          >
            <v-btn color="primary">
              {{ kubernetesConfiguration.kubeconfig ? '완료' : '실행' }}</v-btn
            >
          </SettingItem>
        </SettingGroup>

        <SettingGroup title="애드온">
          <SettingItem subtitle="서비스 공인IP 할당 (L4)">
            <v-btn color="primary" @click="toggleBoolean('kubernetesConfiguration.addon.serviceIP')"
              >실행</v-btn
            >
            <StatusText
              :status="kubernetesConfiguration.addon.serviceIP"
              :text="
                kubernetesConfiguration.addon.serviceIP
                  ? 'L4가 설정되었습니다.'
                  : 'L4가 설정되지 않았습니다.'
              "
            />
          </SettingItem>
          <SettingItem subtitle="HTTP/HTTPS 요청 라우팅 (L7)">
            <v-btn
              color="primary"
              @click="toggleBoolean('kubernetesConfiguration.addon.httpRouting')"
              >실행</v-btn
            >
            <StatusText
              :status="kubernetesConfiguration.addon.httpRouting"
              :text="
                kubernetesConfiguration.addon.httpRouting
                  ? 'L7가 설정되었습니다.'
                  : 'L7가 설정되지 않았습니다.'
              "
            />
          </SettingItem>
          <SettingItem subtitle="Service Mesh">
            <v-btn
              color="primary"
              @click="toggleBoolean('kubernetesConfiguration.addon.serviceMesh')"
              >실행</v-btn
            >
            <StatusText
              :status="kubernetesConfiguration.addon.serviceMesh"
              :text="
                kubernetesConfiguration.addon.serviceMesh
                  ? 'Service Mesh가 설정되었습니다.'
                  : 'Service Mesh가 설정되지 않았습니다.'
              "
            />
          </SettingItem>
          <SettingItem subtitle="클러스터 관리">
            <v-btn
              color="primary"
              @click="toggleBoolean('kubernetesConfiguration.addon.clusterManagement')"
              >실행</v-btn
            >
            <StatusText
              :status="kubernetesConfiguration.addon.clusterManagement"
              :text="
                kubernetesConfiguration.addon.clusterManagement
                  ? '클러스터 관리가 설정되었습니다.'
                  : '클러스터 관리가 설정되지 않았습니다.'
              "
            />
          </SettingItem>
        </SettingGroup>
      </v-card>
    </v-container>
  </v-app>
</template>

<script>
import SettingGroup from './components/SettingGroup.vue'
import SettingItem from './components/SettingItem.vue'
import StatusText from './components/StatusText.vue'

export default {
  components: { SettingItem, SettingGroup, StatusText },
  data() {
    return {
      kubernetesPreconfiguration: {
        CRI: {
          containerd: false,
          cgroup: false,
        },
        kernel: false,
        swap: false,
        firewall: false,
        SELinux: 'enforcing',
      },
      kubernetesConfiguration: {
        masterNode: {
          mode: 'Single Mode',
          HALB: false,
          init: {
            serviceNetwork: '',
            podNetworkCIDR: '192.168.0.0/16',
            podNetworkCIDRList: ['192,168.0.0/16', '10.244.0.0/16'],
          },
        },
        workerNode: {
          add: false,
        },
        CNI: false,
        kubeconfig: false,
        addon: {
          serviceIP: false,
          httpRouting: false,
          serviceMesh: false,
          clusterManagement: false,
        },
      },
    }
  },
  methods: {
    kubernetesConfigurationMasterNodeMode() {
      // 마스터노드 모드를 변경
      this.kubernetesConfiguration.masterNode.mode =
        this.kubernetesConfiguration.masterNode.mode === 'Single Mode' ? 'HA Mode' : 'Single Mode'
    },
    toggleBoolean(path) {
      // 객체 경로를 동적으로 찾아서 값을 토글
      const keys = path.split('.')
      let obj = this
      for (let i = 0; i < keys.length - 1; i++) {
        obj = obj[keys[i]]
      }
      obj[keys[keys.length - 1]] = !obj[keys[keys.length - 1]]
    },
  },
}
</script>

<style scoped>
.v-card {
  max-width: auto;
  margin: auto;
}
</style>
