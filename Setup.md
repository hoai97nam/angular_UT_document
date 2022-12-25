Within this project, Jest is used as a main library for implementing unit test. Besides Jasmin in some cases.
## Installation
### 1. Extension
&nbsp; &nbsp; &nbsp; Download Jest runner on VSCode marketplace.

&nbsp; &nbsp; &nbsp; This extension will support develop to run a test case or debug.

&nbsp; &nbsp; &nbsp; After installing extension from marketplace. Now, let us focus on `.spec.ts` file where developers work mainly within writing unit test.

&nbsp; &nbsp; &nbsp; There is 2 new options `Run` and `Debug` are above describe command. They mean installing successfully.

### 2. Configuration
Default angular test is Jasmine framework, developer should notice at the file configurations because this is Jest, there would be a bit different

This is what `jest.config.ts` looks like:

    module.exports = {
      displayName: 'store-notebook',
      preset: '../../../jest.preset.js',
      setupFilesAfterEnv: ['<rootDir>/src/test-setup.ts'],
      globals: {
        'ts-jest': {
          tsConfig: '<rootDir>/tsconfig.spec.json',
          stringifyContentPathRegex: '\\.(html|svg)$',
          astTransformers: {
            before: [
              'jest-preset-angular/build/InlineFilesTransformer',
              'jest-preset-angular/build/StripStylesTransformer',
            ],
          },
        },
      },
      coverageDirectory: '../../../coverage/libs/store/notebook', // path to export coverage
      snapshotSerializers: [
        'jest-preset-angular/build/AngularNoNgAttributesSnapshotSerializer.js',
        'jest-preset-angular/build/AngularSnapshotSerializer.js',
        'jest-preset-angular/build/HTMLCommentSerializer.js',
      ],
    };
    
This `tsconfig.spec.json` file

    {
      "extends": "./tsconfig.json",
      "compilerOptions": {
        "outDir": "../../../dist/out-tsc",
        "module": "commonjs",
        "types": ["jest", "node"]
      },
      "files": ["src/test-setup.ts"],
      "include": ["**/*.spec.ts", "**/*.d.ts"]
    }
    
- Type with `jest` allow developers to use jest directly as global variable in some use cases

`test-setup.ts` file:

        import 'jest-preset-angular';
Make sure that 3 above files have been created before run unit test
